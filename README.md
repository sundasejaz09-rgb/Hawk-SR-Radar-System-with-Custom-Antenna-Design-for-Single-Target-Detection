function plot_results(params, rd_map, detections, threshold, range_profile)

    range_axis = (0:size(range_profile,1)-1) * params.c/(2*params.fs);
    
    doppler_axis = linspace(-params.PRF/2, params.PRF/2, size(rd_map,2));
    
    figure('Name','Range Profile (Single Pulse)');
    
    plot(range_axis, 20*log10(abs(range_profile(:,1))));
    
    xlabel('Range (m)'); ylabel('Power (dB)');
    
    title('Range Profile'); grid on;
    
    figure('Name','Range-Doppler Map');
    
    imagesc(doppler_axis, range_axis, 10*log10(rd_map));
    
    xlabel('Doppler (Hz)'); ylabel('Range (m)');
    
    title('Range-Doppler Map'); colorbar;
    
    % Overlay detections
    
    hold on;
    [r_idx, d_idx] = find(detections);
    
    plot(doppler_axis(d_idx), range_axis(r_idx), 'rx', 'MarkerSize', 10, 'LineWidth', 2);
    
    hold off;
    
end
