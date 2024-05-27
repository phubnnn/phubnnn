- function varargout = Final_Project(varargin)
% FINAL_PROJECT MATLAB code for Final_Project.fig
%      FINAL_PROJECT, by itself, creates a new FINAL_PROJECT or raises the existing
%      singleton*.
%
%      H = FINAL_PROJECT returns the handle to a new FINAL_PROJECT or the handle to
%      the existing singleton*.
%
%      FINAL_PROJECT('CALLBACK',hObject,eventData,handles,...) calls the local
%      function named CALLBACK in FINAL_PROJECT.M with the given input arguments.
%
%      FINAL_PROJECT('Property','Value',...) creates a new FINAL_PROJECT or raises the
%      existing singleton*.  Starting from the left, property value pairs are
%      applied to the GUI before Final_Project_OpeningFcn gets called.  An
%      unrecognized property name or invalid value makes property application
%      stop.  All inputs are passed to Final_Project_OpeningFcn via varargin.
%
%      *See GUI Options on GUIDE's Tools menu.  Choose "GUI allows only one
%      instance to run (singleton)".
%
% See also: GUIDE, GUIDATA, GUIHANDLES

% Edit the above text to modify the response to help Final_Project

% Last Modified by GUIDE v2.5 27-May-2024 11:52:07

% Begin initialization code - DO NOT EDIT
gui_Singleton = 1;
gui_State = struct('gui_Name',       mfilename, ...
                   'gui_Singleton',  gui_Singleton, ...
                   'gui_OpeningFcn', @Final_Project_OpeningFcn, ...
                   'gui_OutputFcn',  @Final_Project_OutputFcn, ...
                   'gui_LayoutFcn',  [] , ...
                   'gui_Callback',   []);
if nargin && ischar(varargin{1})
    gui_State.gui_Callback = str2func(varargin{1});
end

if nargout
    [varargout{1:nargout}] = gui_mainfcn(gui_State, varargin{:});
else
    gui_mainfcn(gui_State, varargin{:});
end
% End initialization code - DO NOT EDIT
end

% --- Executes just before Final_Project is made visible.
function Final_Project_OpeningFcn(hObject, eventdata, handles, varargin)
% This function has no output args, see OutputFcn.
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
% varargin   command line arguments to Final_Project (see VARARGIN)

% Choose default command line output for Final_Project
handles.output = hObject;

% Update handles structure
guidata(hObject, handles);

% UIWAIT makes Final_Project wait for user response (see UIRESUME)
% uiwait(handles.figure1);
global a;
a = arduino();
end 

% --- Outputs from this function are returned to the command line.
function varargout = Final_Project_OutputFcn(hObject, eventdata, handles) 
delete(instrfind({'Port'},{'COM3'}))
clear a;
% varargout  cell array for returning output args (see VARARGOUT);
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)

% Get default command line output from handles structure
varargout{1} = handles.output;


function lightIntensity_Callback(hObject, eventdata, handles)
% hObject    handle to lightIntensity (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)

% Hints: get(hObject,'String') returns contents of lightIntensity as text
%        str2double(get(hObject,'String')) returns contents of lightIntensity as a double
 % Get the light intensity value from the edit field
    lightIntensity = str2double(get(hObject, 'String'));
    % Check if the light intensity is valid
    if isnan(lightIntensity) || lightIntensity < 0 || lightIntensity > 100
        errordlg('Enter a value between 0 and 100.', 'Error');
        return;
    end
    % Update the light intensity on the red LED
    analogWrite(handles.arduino, 'D9', lightIntensity);
    % Update the light intensity display field
    set(handles.lightIntensityDisplay, 'String', sprintf('%.1f%%', lightIntensity));
    % Update handles structure
    guidata(hObject, handles);
end
function light_intensity_CreateFcn(hObject, ~, ~)
    % Set the background color of the edit control on Windows
    if ispc && isequal(get(hObject,'BackgroundColor'), get(0,'defaultUicontrolBackgroundColor'))
        set(hObject,'BackgroundColor','white');
    end
end
    % Create the main figure
    handles.figure = figure('Position', [100, 100, 400, 300]);
    % Create the edit field for light intensity input
    handles.lightIntensityEdit = uicontrol('Style', 'edit', 'Position', [50, 200, 100, 30], 'Callback', @light_intensity_Callback);
    % Create the static text field to display the light intensity percentage
    handles.lightIntensityDisplay = uicontrol('Style', 'text', 'Position', [200, 200, 100, 30], 'String', '0.0%', 'BackgroundColor', 'white');
    % Store handles in the figure
    guidata(handles.figure, handles);
end
% Main entry point

% --- Executes during object creation, after setting all properties.
function lightIntensity_CreateFcn(hObject, eventdata, handles)
% hObject    handle to lightIntensity (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    empty - handles not created until after all CreateFcns called

% Hint: edit controls usually have a white background on Windows.
%       See ISPC and COMPUTER.
if ispc && isequal(get(hObject,'BackgroundColor'), get(0,'defaultUicontrolBackgroundColor'))
    set(hObject,'BackgroundColor','white');
end
end


% --- Executes on button press in Run_button.
function Run_button_Callback(hObject, eventdata, handles)
% hObject    handle to Run_button (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
global a;
a=arduino();
IR= 'A0' ;
Red= 'D7';
numsamples = str2double(get(handles.samples, 'String'));
%Read IR sensor data
IR_data = zeros(1, samples);
Detection_data=zeros(3,1);
IR_sensor=readVoltage(handles.arduino);

% update and plot IR senser data
i=1:numsamples
IR_data(i)=IR_sensor;
axes(handles.axes1.IR_Graph);
plot(handles.axes1,IR_data);
xlable(handles.axes1,'sample');
ylabel(handles.axes1,'IR_data');
title(handles.axes1,'Change of IR data');
grid on
drawnow;
end


%input function to read objection data
    function Detection_data = updateObjectDetection_data(IR_sensor, Detection_data);
while 1
IR_sensor=readVoltage(arduino,IR);
if IR_sensor < 1.25 && IR_sensor == 1.25
    Detection_data(1)=Detection_data(1)+1;
elseif IR_sensor >1.25 && IR_sensor < 3.75
    Detection_data(2)= Detection_data(2)+1;
else IR_sensor >= 3.75 && IR_sensor <= 5
    Detection_data(3)= Detection_data(3)+1
    
end
end
%plot Object detection data
bar(handles.axes2,Detection_data_Graph);
set(handles.axes2,'xticklabel','low','Mid','High');
xlabel(hadles.axes2,'detection region');
ylabel(handles.axes2,'Number of counts');
title(handles.axes2,'Analysis of object detection');
grid on
end


% --- Executes on button press in Clear_button.
function Clear_button_Callback(hObject, eventdata, handles)
% hObject    handle to Clear_button (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
set(handles.sample,'string','');
set(handles.lightIntensity,'string','');
cla(handles.axes1);
cla(handles.axes2);
end

% --- Executes on button press in Turnoff_button.
function Turnoff_button_Callback(hObject, eventdata, handles)
% hObject    handle to Turnoff_button (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
get(handles.Turnoff_button,'string');
global a;
writeDigitalPin(a,'D7',0);
end

% --- Executes on button press in Ttttturnon_button.
function Ttttturnon_button_Callback(hObject, eventdata, handles)
% hObject    handle to Ttttturnon_button (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
get(handles.Ttttturnon_button,'string');
global a;
writeDigitalPin(a,'D7',1);
end

function sample_Callback(hObject, eventdata, handles)
% hObject    handle to lightIntensity (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)

% Hints: get(hObject,'String') returns contents of lightIntensity as text
%        str2double(get(hObject,'String')) returns contents of lightIntensity as a double
numSample=str2double(get(handles.sample,'string'));
handles.numSample=numSample;
guidata(hObject,handles);
end

% --- Executes during object creation, after setting all properties.
function sample_CreateFcn(hObject, eventdata, handles)
% hObject    handle to lightIntensity (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    empty - handles not created until after all CreateFcns called


% Hint: edit controls usually have a white background on Windows.
%       See ISPC and COMPUTER.
if ispc && isequal(get(hObject,'BackgroundColor'), get(0,'defaultUicontrolBackgroundColor'))
    set(hObject,'BackgroundColor','white');
end
end
