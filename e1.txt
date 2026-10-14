% Experiment 1: Simulation of Large Scale Fading
% Lab Index Experiment No.: 8
clear;
clc;
clf;
fc = 1.5e9;      % Carrier frequency in Hz
d0 = 100;        % Reference distance in meters
sigma = 3;       % Standard deviation for log-normal shadowing in dB
distance = (1:2:31).^2;
Gt = [1 1 0.5];      % Transmitter gains
Gr = [1 0.5 0.5];    % Receiver gains
Exp = [2 3 6];       % Path loss exponents
for k = 1:3
    y_Free(k,:) = PL_free(fc, distance, Gt(k), Gr(k));
    y_logdist(k,:) = PL_logdist_or_norm(fc, distance, d0, Exp(k));
    y_lognorm(k,:) = PL_logdist_or_norm(fc, distance, d0, Exp(1), sigma);
end
figure(1)
semilogx(distance, y_Free(1,:), 'k-o', ...
         distance, y_Free(2,:), 'k-^', ...
         distance, y_Free(3,:), 'k-');
grid on;
axis([1 1000 40 110]);
title(['Free Path-loss Model, f_c = ', num2str(fc/1e6), ' MHz']);
xlabel('Distance [m]');
ylabel('Path loss [dB]');
legend('G_t = 1, G_r = 1', ...
       'G_t = 1, G_r = 0.5', ...
       'G_t = 0.5, G_r = 0.5');
figure(2)
semilogx(distance, y_logdist(1,:), 'k-o', ...
         distance, y_logdist(2,:), 'k-^', ...
         distance, y_logdist(3,:), 'k-s');
grid on;
axis([1 1000 40 110]);
title(['Log-distance Path-loss Model, f_c = ', num2str(fc/1e6), ' MHz']);
xlabel('Distance [m]');
ylabel('Path loss [dB]');
legend('n = 2', 'n = 3', 'n = 6');
figure(3)
semilogx(distance, y_lognorm(1,:), 'k-o', ...
         distance, y_lognorm(2,:), 'k-^', ...
         distance, y_lognorm(3,:), 'k-s');
grid on;
axis([1 1000 40 110]);
title(['Log-normal Path-loss Model, f_c = ', ...
       num2str(fc/1e6), ' MHz, \sigma = ', ...
       num2str(sigma), ' dB, n = 2']);
xlabel('Distance [m]');
ylabel('Path loss [dB]');
legend('Path 1', 'Path 2', 'Path 3');
function PL = PL_free(fc, d, Gt, Gr)
    lambda = 3e8 / fc;                  % Wavelength
    tmp = lambda ./ (4 * pi * d);       % Basic path loss equation
    if nargin > 2
        tmp = tmp * sqrt(Gt);           % Incorporate transmitter gain
    end
    if nargin > 3
        tmp = tmp * sqrt(Gr);           % Incorporate receiver gain
    end
    PL = -20 * log10(tmp);              % Convert to dB
end
function PL = PL_logdist_or_norm(fc, d, d0, n, sigma)
    lambda = 3e8 / fc;    % Wavelength
    PL = -20 * log10(lambda / (4 * pi * d0)) + ...
         10 * n * log10(d / d0);
    if nargin > 4
        PL = PL + sigma * randn(size(d));
    end
end
