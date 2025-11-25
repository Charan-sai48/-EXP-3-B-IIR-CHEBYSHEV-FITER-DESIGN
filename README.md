# EXP 3 : IIR-CHEBYSHEV-FITER-DESIGN

## AIM: 

 To design an IIR Chebyshev filter  using SCILAB. 

## APPARATUS REQUIRED: 
PC installed with SCILAB. 

## PROGRAM (LPF): 
```
clear; clc; close;
// ========== INPUT PARAMETERS ==========
wp = 0.2 * %pi;      // Pass band frequency (Radians) - 0.2π
ws = 0.4 * %pi;      // Stop band frequency (Radians) - 0.4π
alphap = 1;          // Pass band attenuation (dB) - 1 dB ripple
alphas = 40;         // Stop band attenuation (dB) - 40 dB
T = 1;               // Sampling time (normalized to 1)

disp('Filter Specifications:');
disp(wp, 'Pass band frequency wp = ');
disp(ws, 'Stop band frequency ws = ');
disp(alphap, 'Pass band attenuation = ');
disp(alphas, 'Stop band attenuation = ');

// ========== PRE-WARPING (Digital to Analog) ==========
omegap = (2/T) * tan(wp/2);
omegas = (2/T) * tan(ws/2);

disp('Pre-warped frequencies:');
disp(omegap, 'omegap = ');
disp(omegas, 'omegas = ');

// ========== CALCULATE FILTER ORDER ==========
N = acosh(sqrt(((10^(0.1*alphas)) - 1) / ((10^(0.1*alphap)) - 1))) / acosh(omegas/omegap);
disp(N, 'Calculated order N = ');

N = ceil(N);  // Round up to nearest integer
disp(N, 'Rounded order N = ');

// ========== CALCULATE CUTOFF FREQUENCY ==========
omegac = omegap / (((10^(0.1*alphap)) - 1)^(1/(2*N)));
disp(omegac, 'Cutoff frequency omegac = ');

// ========== CALCULATE RIPPLE PARAMETER ==========
Epsilon = sqrt((10^(0.1*alphap)) - 1);
disp(Epsilon, 'Ripple parameter Epsilon = ');

// ========== DESIGN ANALOG CHEBYSHEV FILTER ==========
[pols, gn] = zpch1(N, Epsilon, omegap);

disp('Analog filter parameters:');
disp(gn, 'Gain = ');
disp(pols, 'Poles = ');

// Create analog transfer function H(s)
hs = poly(gn, 's', 'coeff') / real(poly(pols, 's'));
disp(hs, 'Analog Chebyshev filter H(s) = ');

// ========== BILINEAR TRANSFORMATION (Analog to Digital) ==========
z = poly(0, 'z');
Hz = horner(hs, (2/T) * ((z-1)/(z+1)));
disp(Hz, 'Digital filter H(z) = ');

// ========== FREQUENCY RESPONSE PLOT ==========
HW = frmag(Hz, 512);
w = 0:%pi/511:%pi;

figure(1);
plot(w/%pi, abs(HW), 'b-', 'LineWidth', 2);
xlabel('Normalized Digital Frequency (w/π)');
ylabel('Magnitude');
title('Frequency Response of Chebyshev IIR Low-Pass Filter');
xgrid();

// Add passband and stopband reference lines
a = gca();
a.data_bounds = [0, 0; 1, max(abs(HW))*1.1];
xpoly([wp/%pi, wp/%pi], [0, max(abs(HW))], 'lines');
e = gce();
e.foreground = color('red');
e.line_style = 2;

disp('Filter design completed successfully!');
```

## PROGRAM (HPF): 
```
// Simplified IIR Chebyshev High-Pass Filter Design
clear; clc; close;

// ========== INPUT PARAMETERS ==========
// You can change these values as needed
wp = 0.6 * %pi;      // Pass band frequency (Radians) - 0.6π
ws = 0.4 * %pi;      // Stop band frequency (Radians) - 0.4π
alphap = 1;          // Pass band attenuation (dB) - 1 dB ripple
alphas = 40;         // Stop band attenuation (dB) - 40 dB
T = 1;               // Sampling time (normalized to 1)

disp('High-Pass Filter Specifications:');
disp(wp, 'Pass band frequency wp = ');
disp(ws, 'Stop band frequency ws = ');
disp(alphap, 'Pass band attenuation = ');
disp(alphas, 'Stop band attenuation = ');

// ========== PRE-WARPING (Digital to Analog) ==========
omegap = (2/T) * tan(wp/2);
omegas = (2/T) * tan(ws/2);

disp('Pre-warped frequencies:');
disp(omegap, 'omegap = ');
disp(omegas, 'omegas = ');

// ========== CALCULATE FILTER ORDER ==========
// For HPF: use omegas/omegap ratio (reversed from LPF)
N = acosh(sqrt(((10^(0.1*alphas)) - 1) / ((10^(0.1*alphap)) - 1))) / acosh(omegap/omegas);
disp(N, 'Calculated order N = ');

N = ceil(N);  // Round up to nearest integer
disp(N, 'Rounded order N = ');

// ========== CALCULATE CUTOFF FREQUENCY ==========
omegac = omegap / (((10^(0.1*alphap)) - 1)^(1/(2*N)));
disp(omegac, 'Cutoff frequency omegac = ');

// ========== CALCULATE RIPPLE PARAMETER ==========
Epsilon = sqrt((10^(0.1*alphap)) - 1);
disp(Epsilon, 'Ripple parameter Epsilon = ');

// ========== DESIGN ANALOG LOW-PASS PROTOTYPE ==========
[pols, gn] = zpch1(N, Epsilon, omegap);

disp('Analog prototype filter parameters:');
disp(gn, 'Gain = ');
disp(pols, 'Poles = ');

// Create analog low-pass transfer function H(s)
hs_lp = poly(gn, 's', 'coeff') / real(poly(pols, 's'));
disp(hs_lp, 'Analog Low-Pass prototype H(s) = ');

// ========== LOW-PASS TO HIGH-PASS TRANSFORMATION ==========
// Replace s with (omegac^2 / s) for LP to HP transformation
s = poly(0, 's');
hs_hp = horner(hs_lp, omegac^2 / s);
disp(hs_hp, 'Analog High-Pass filter H(s) = ');

// ========== BILINEAR TRANSFORMATION (Analog to Digital) ==========
z = poly(0, 'z');
Hz = horner(hs_hp, (2/T) * ((z-1)/(z+1)));
disp(Hz, 'Digital High-Pass filter H(z) = ');

// ========== FREQUENCY RESPONSE PLOT ==========
HW = frmag(Hz, 512);
w = 0:%pi/511:%pi;

figure(1);
plot(w/%pi, abs(HW), 'b-', 'LineWidth', 2);
xlabel('Normalized Digital Frequency (w/π)');
ylabel('Magnitude');
title('Frequency Response of Chebyshev IIR High-Pass Filter');
xgrid();

// Add passband reference line
a = gca();
a.data_bounds = [0, 0; 1, max(abs(HW))*1.1];
xpoly([wp/%pi, wp/%pi], [0, max(abs(HW))], 'lines');
e = gce();
e.foreground = color('red');
e.line_style = 2;

disp('High-Pass Filter design completed successfully!');
```

## OUTPUT (LPF) : 

<img width="670" height="953" alt="image" src="https://github.com/user-attachments/assets/bd5b22f4-2172-4e9e-823f-7a36701a9cf0" />

<img width="1034" height="699" alt="image" src="https://github.com/user-attachments/assets/e158e567-6032-4a1f-bca0-a9a5e6042151" />


<img width="764" height="627" alt="image" src="https://github.com/user-attachments/assets/255fe4fd-0372-4007-b1a9-05a17f85623e" />




## OUTPUT (HPF) : 

<img width="671" height="944" alt="image" src="https://github.com/user-attachments/assets/4de32e56-b629-4f4a-a213-e8797501f99b" />

<img width="1147" height="867" alt="image" src="https://github.com/user-attachments/assets/5a8e1d0c-c42d-40a3-97c8-a69dceedc593" />


<img width="767" height="612" alt="image" src="https://github.com/user-attachments/assets/968583a0-68e6-4904-8e3f-d4977dded46c" />



## RESULT: 

Thus, the IIR Chebysheve filter (LPF & HPF) using SCILAB was successfully executed and the output is verified.
