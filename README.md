function [waveform, time_axis] = generate_waveform(params)

    fs = params.fs;
    
    Tp = params.Tp;
    
    B  = params.B;

    % Time vector for one pulse
    
    t = (0:1/fs:Tp-1/fs).';
    
    time_axis = t;

    % LFM chirp (complex baseband)
    
    slope = B / Tp;
    
    waveform = exp(1j * pi * slope * t.^2);

    % Normalize to unit energy
    
    waveform = waveform / norm(waveform);
    
end
