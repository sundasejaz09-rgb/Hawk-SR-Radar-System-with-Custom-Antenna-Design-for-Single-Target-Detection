function params = radar_parameters()

    % Waveform parameters
    params.fc = 2.1e9;              % Carrier frequency (Hz) S-band
    
    params.B  = 100e3;              % Bandwidth (Hz)
    
    params.Tp = 10e-6;              % Pulse width (s)
    
    params.PRF = 10e3;              % Pulse repetition frequency (Hz)
    
    params.fs = 200e6;              % Sampling frequency (Hz)
    
    params.waveform = 'LFM';        % Linear frequency modulation

    % Transmitter
    
    params.Pt = 5000;               % Peak power (W)
    
    params.Gt = 30;                 % Transmit antenna gain (dBi)
    
    params.Gr = 30;                 % Receive antenna gain (dBi)
    
    params.L  = 0;                  % System losses (dB)

    % Receiver
    
    params.NF = 3;                  % Noise figure (dB)
    
    params.T0 = 290;                % Reference temperature (K)
    
    params.RxGain = 20;             % Receiver gain (dB)

    % Antenna
    
    params.antenna_type = 'CustomParabolic';
    
    params.beamwidth_az = 3.5;      % Azimuth beamwidth (deg)
    
    params.beamwidth_el = 3.5;      % Elevation beamwidth (deg)

    % Propagation
    
    params.c = physconst('LightSpeed');
    
    params.lambda = params.c / params.fc;

    % Detection & Tracking
    
    params.Pfa = 1e-6;              % Desired probability of false alarm
    
    params.num_guard_cells = 2;
    
    params.num_training_cells = 10;
    
    params.track_confirmation = [2 3]; % M/N logic
    
    params.track_deletion = [5 5];      % P/R logic

    % Simulation
    
    params.max_range = 15e3;         % Maximum range of interest (m)
    
    params.range_bins = round(2*params.max_range/params.c * params.fs);
    
end
