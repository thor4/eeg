# eeg

clc

config_io;

for k = 1:15
    for i = 1:5
        sendPortCode(k,4, 888, 0);
        pause(1)
    end
end
%4, 2, 6, r4,


function sendPortCode(PPCode,pulseLength, PPAddressOut, debug)
%% Send port codes through parallel port for a specified time
%
% All inputs are optional, skip an input with "[]".
%
% Inputs:
%   PPCode = The trigger code you want to send (int in range 1:256)
%
%   pulseLength = The length of the pulse in ms (pauses the script
%       while sending the pulse, should not be higher than 5 ms)
%
%   PPAddressOut = The port adress you want to send a code to (in
%       decimal code, use hex2dec() if you only have the hex code)
%
%   debug = flag to indicate if you are debugging or not
%
% Default values:
%   PPCode = 10
%   pulseLength = 3
%   PPAddressOut = 884 (decimal number for port code, 884 corresponds
%       with 0x374)
%   debug = 0 (not debugging, set to 1 if debugging)
%
% Usage:
%   sendPortCode(10,3, 884, 0)
%       This sends the trigger "10" for 3ms to port 884
%
% Written by: Jonathan van Leeuwen 2015, Vu


%% Input handeling
if ~exist('PPAddressOut','var') || isempty(PPAddressOut)
    PPAddressOut = 884;
end;
if ~exist('pulseLength','var') || isempty(pulseLength)
    pulseLength = 3;
end;
if ~exist('PPCode','var') || isempty(PPCode)
    PPCode = 10;
end;
if ~exist('debug','var') || isempty(debug)
    debug = 0;
end;

%% Timing warnings, if a warning is displayed it maks the function about 2 ms slower
if pulseLength > 5
    warning('Puleses larger than 5ms might cause timing issues, check your timing.');
end
if debug > 0
    warning('Debugging, no pulses sent and timing might be longer.');
end
if mod(PPCode,1) ~= 0 || PPCode > 256 || PPCode < 1
    debug = 1;
    warning('Your pulse code was not within the 1:256 range or was not an integer, no pulse was sent.')
end

%% Do the things
if debug == 0
    % Send code
    outp(PPAddressOut,PPCode);
    
    % Pause for x time
    pause(pulseLength/1000);
    
    % Reset port code
    outp(PPAddressOut,0);
else
    % Pause for x time
    WaitSecs(pulseLength/1000);
    % Check inputs
    disp(['Port Code: ' num2str(PPCode) '  Pulse Length: ' num2str(pulseLength) '  PPAddress: ' num2str(PPAddressOut)])
end
end

