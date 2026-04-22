function pattern = antenna_pattern(params, az_angles, el_angles)

    if nargin < 2
        az_angles = -180:1:180;
        
        el_angles = -90:1:90;
        
    end

    beamwidth_az = params.beamwidth_az;
    
    beamwidth_el = params.beamwidth_el;

    [AZ, EL] = meshgrid(az_angles, el_angles);

    % Convert to linear gains (dBi to linear)
    
    G_linear = 10^(params.Gt/10);

    % Gaussian beam model
    
    sigma_az = beamwidth_az / (2*sqrt(2*log(2)));
    
    sigma_el = beamwidth_el / (2*sqrt(2*log(2)));

    pattern = G_linear * exp( - (AZ.^2)/(2*sigma_az^2) - (EL.^2)/(2*sigma_el^2) );

    % Optional: plot the pattern
    
    if nargout == 0
    
        figure;
        
        surf(AZ, EL, 10*log10(pattern));
        
        xlabel('Azimuth (deg)'); ylabel('Elevation (deg)');
        
        zlabel('Gain (dBi)'); title('Custom Antenna Pattern');
        
        shading interp; colorbar;
        
    end
    
end
