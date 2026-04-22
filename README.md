function [detections, tracks] = run_radar_simulation(params, target_range, target_velocity)

    if nargin < 2
    
        target_range = 5000;      % 5 km
        
        target_velocity = 50;     % m/s
        
    end

    % ---- Waveform generation ----
    
    [pulse, t_pulse] = generate_waveform(params);
    
    num_pulses = 64;              % Number of pulses in CPI
    
    tx_wave = repmat(pulse, num_pulses, 1);

    % ---- Simulate target echo ----
    
    fs = params.fs;
    
    c  = params.c;
    
    fc = params.fc;
    
    lambda = params.lambda;
    
    Pt = params.Pt;
    
    Gt = 10^(params.Gt/10);
    
    Gr = 10^(params.Gr/10);
    
    L  = 10^(params.L/10);
    
    NF = 10^(params.NF/10);
    
    k  = physconst('Boltzmann');
    
    T0 = params.T0;
    
    RxGain = 10^(params.RxGain/10);

    % Free space path loss (two-way)
    
    R = target_range;
    
    path_loss = (lambda^2) / ((4*pi)^3 * R^4 * L);
    
    % Received power for a 1 m² RCS
    
    sigma = 1;                     % Target RCS (m²)
    
    Pr = Pt * Gt * Gr * path_loss * sigma;

    % Delay and Doppler
    
    tau = 2 * R / c;
    
    fd  = 2 * target_velocity / lambda;
    
    t_total = (0:length(tx_wave)-1)' / fs;
    
    t_delayed = t_total - tau;
    
    % Baseband echo with phase rotation (simplified)
    
    echo = sqrt(Pr) * interp1(t_pulse, pulse, t_delayed, 'linear', 0) ...
    
           .* exp(1j * 2*pi * fd * t_total);

    % ---- Receiver noise ----
    
    Bn = params.B;                 % Noise bandwidth
    
    N0 = k * T0 * NF;
    
    noise_power = N0 * Bn;
    
    noise = sqrt(noise_power/2) * (randn(size(echo)) + 1j*randn(size(echo)));
    
    rx_signal = echo * RxGain + noise;

    % ---- Matched filter (pulse compression) ----

    matched_filter = conj(flipud(pulse));
    
    mf_output = conv(rx_signal, matched_filter, 'same');

    % Reshape into fast-time/slow-time matrix
    
    samples_per_pulse = length(pulse);
    
    range_profile = reshape(mf_output, samples_per_pulse, num_pulses);

    % ---- CFAR detection on range-Doppler map ----
    
    range_doppler = fftshift(fft(range_profile, [], 2), 2);
    
    rd_map = abs(range_doppler).^2;

    % CA-CFAR
    pfa = params.Pfa;
    
    guard = params.num_guard_cells;
    
    train = params.num_training_cells;
    
    threshold = cfar_threshold(rd_map, pfa, guard, train);

    detections = rd_map > threshold;

    % ---- Multi-object tracking (simplified) ----
    
    % Use MATLAB's trackerGNN if available; else create simple Kalman
    
    if license('test', 'Sensor_Fusion_and_Tracking_Toolbox')
    
        tracks = simulate_tracker(detections, params);
        
    else
        tracks = simple_kalman_track(detections);
        
    end

    % ---- Visualisation ----
    
    plot_results(params, rd_map, detections, threshold, range_profile);
    
end

function thr = cfar_threshold(data, pfa, guard, train)
    % Simple cell-averaging CFAR
    
    [N,M] = size(data);
    
    thr = zeros(size(data));
    
    win_size = 2*(guard+train)+1;
    
    for r = guard+train+1 : N-guard-train
    
        for d = guard+train+1 : M-guard-train
        
            % Extract training cells (exclude guard cells and CUT)
            
            cut = data(r,d);
            
        end
        
    end
    
    % Placeholder: use constant threshold based on noise estimate
    
    noise_power = mean(data(:));
    
    thr(:) = noise_power * chi2inv(1-pfa, 2);
    
end
