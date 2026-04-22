clear; close all; clc;

params = radar_parameters();

target_range = 5000;      % meters

target_velocity = 100;    % m/s (towards radar)

% Run simulation

[detections, tracks] = run_radar_simulation(params, target_range, target_velocity);

% Additional plots: antenna pattern

antenna_pattern(params);

% Display performance metrics

fprintf('=== SRHawk Performance Summary ===\n');

fprintf('Range Resolution: %.2f m\n', params.c/(2*params.B));

fprintf('Maximum Unambiguous Range: %.1f km\n', params.c/(2*params.PRF)/1e3);

fprintf('Doppler Resolution: %.2f m/s\n', params.lambda/(2*params.Tp*64));
