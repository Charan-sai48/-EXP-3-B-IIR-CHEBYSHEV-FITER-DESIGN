# EXP 3 : IIR-CHEBYSHEV-FITER-DESIGN

## AIM: 

 To design an IIR Chebyshev filter  using SCILAB. 

## APPARATUS REQUIRED: 
PC installed with SCILAB. 

## PROGRAM (LPF): 
```
clc ;
close ;
wp=input('Enter the pass band frequency (Radians )= ' );
ws=input('Enter the stop band frequency (Radians )= ' );
alphap=input( ' Enter the pass band attenuation (dB)=' );
alphas=input( ' Enter the stop band attenuation(dB)=' );
T=input('Enter the Value of sampling Time=');
omegap=(2/T)*tan(wp/2);
disp(omegap,'omegap=');
omegas=(2/T)*tan(ws/2);
disp(omegas,'omegas=');
N=acosh(sqrt(((10^(0.1alphas))-1)/((10^(0.1alphap))-1)))/(acosh(omegas/omegap));
disp(N,'N=');
N=ceil(N);
disp(N,'Round off value of N=');
omegac=omegap/(((10^(0.1alphap)) -1)^(1/(2 N)));
disp(omegac,'omegac=');
Epsilon = sqrt ((10^(0.1*alphap))-1);
disp(Epsilon,'Epsilon=');
[pols ,gn] = zpch1(N, Epsilon,omegap );
disp(gn,'Gain');
disp(pols,'Poles');
hs=poly(gn,'s','coeff')/real(poly(pols,'s'));
disp(hs,'Analog Low pass Chebyshev Filter Transfer function');
z=poly(0,'z');
Hz=horner(hs,(2/ T)*((z -1)/(z+1)))
disp(Hz,'Digital LPF Transfer function H(Z)=');
HW=frmag(Hz,512);
w=0:%pi/511:%pi ;
plot(w/%pi,abs(HW));
xlabel(' Normalized Digital Frequency w');
ylabel('Magnitude ');
title(' Frequency Response of Chebyshev IIR LPF');
```

## PROGRAM (HPF): 
```
clc;
close;
wp = input('Enter the pass band frequency (Radians )= ');
ws = input('Enter the stop band frequency (Radians )= ');
alphap = input('Enter the pass band attenuation (dB)= ');
alphas = input('Enter the stop band attenuation (dB)= ');
T = input('Enter the Value of sampling Time= ');
// Pre-warping (Bilinear Transformation)
omegap = (2/T)*tan(wp/2); // Passband edge (analog)
disp(omegap,'omegap=');
omegas = (2/T)*tan(ws/2); // Stopband edge (analog)
disp(omegas,'omegas=');
// Order of the HPF
// For HPF: use acosh(omegap/omegas)
N = acosh(sqrt(((10^(0.1alphas))-1)/((10^(0.1alphap))-1))) / acosh(omegap/omegas);
disp(N,'N=');
N = ceil(N);
disp(N,'Round off value of N=');
// Cutoff frequency
omegac = omegap / (((10^(0.1alphap))-1)^(1/(2N)));
disp(omegac,'omegac=');
// Chebyshev Prototype (Normalized LPF)
Epsilon = sqrt((10^(0.1*alphap))-1);
disp(Epsilon,'Epsilon=');
[pols, gn] = zpch1(N, Epsilon, 1); // normalized LPF prototype at 1 rad/s
disp(gn,'Gain');
disp(pols,'Poles');
s = poly(0,'s'); // Laplace variable
hs = poly(gn,'s','coeff') / real(poly(pols,'s'));
disp(hs,'Analog Normalized Chebyshev LPF');
// LPF → HPF Transformation (s -> omegac/s)
sh = horner(hs, omegac/s);
disp(sh,'Analog Chebyshev High-Pass Filter');
// Bilinear Transformation: s -> (2/T)*((z-1)/(z+1))
z = poly(0,'z'); // z-domain variable
Hz = horner(sh, (2/T) * ((z-1)/(z+1)));
disp(Hz,'Digital HPF Transfer function H(Z)=');
// Frequency Response
HW = frmag(Hz, 512);
w = 0:%pi/511:%pi;
plot(w/%pi, abs(HW));
xlabel('Normalized Digital Frequency ×π rad/sample');
ylabel('Magnitude');
title('Frequency Response of Chebyshev IIR High-Pass Filter');
```

## OUTPUT (LPF) : 

<img width="1033" height="602" alt="image" src="https://github.com/user-attachments/assets/25ab7a2c-72b3-4c8a-841b-0d9cab6ff9d5" />

<img width="1040" height="565" alt="image" src="https://github.com/user-attachments/assets/2c7f5c6a-6e7e-4103-87db-4b93d06f8fd4" />

<img width="1031" height="552" alt="image" src="https://github.com/user-attachments/assets/3fd2f029-1373-4faf-8a2a-45fe272b564c" />



## OUTPUT (HPF) : 

<img width="1040" height="557" alt="image" src="https://github.com/user-attachments/assets/723d67c7-4a50-47d4-8773-ec75ba7f12fd" />

<img width="1041" height="556" alt="image" src="https://github.com/user-attachments/assets/3ca3cbde-c0d5-4562-ad45-25aeead7945f" />

<img width="1035" height="616" alt="image" src="https://github.com/user-attachments/assets/a4a9c875-3277-415d-91f8-d63a8ce9685d" />


## RESULT: 

Thus, the IIR Chebysheve filter (LPF & HPF) using SCILAB was successfully executed and the output is verified.
