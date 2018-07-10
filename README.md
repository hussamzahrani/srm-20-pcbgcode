# srm-20-pcbgcode

if you are like me , stuck with a Roland SRM-20, and trying to  export gcode directly from EagleCad ( Autodesk Eagle ) using Pcb-Gcode ULP , you'll have to edit the resulting files to make it compatible with Roland SRM-20

I went though few instructables and tutorials online that suggested other solutions , but I found it time consuming , going through a weird pipeline of exporting as an image then converting it to gcode through diffrent methods

after few hours of going through the gcode specification manual for SRM-20 , I went through the source files and changed it as best as I can

# Usage

you can follow the included documentation for setup , the only diffrence is that there is only one profile to pick (SRM-20) during the setup

just double check during the setup process that "use user gcode (from user-gcode.h)" is checked , you can find it under GCode options tap

after that the only thing you need to do is to use "G54" coordinate system when using the machine contol panel "VPANEL for SRM-20" software
also dont forget to select "G54" when setting origin point

# Modification

you dont need to edit anything in the files or during the setup , but in case there was a new version of PCB-gcode ULP , and you wanted to edit it to make it compatible , here is the details of all the alteration to the original PCB-Gcode are listed below

I started by editing
Profile/generic.h
and saving it as srm-20.h

Line 45

    string DWELL    = "G04 " + PARAM + "%f" + EOL;

this line Calls PARAM which is the parameter for time unit for dwelling

Line 26

    string PARAM      = "P";

the default value was "P" , changed to "X" , as specified for seconds in the machine manual

Line 52

    string END_PROGRAM    = "M02" + EOL;

Changed to 

    string END_PROGRAM    = "M02" + EOL + "%% " + EOL;

to append the % symbol to the end of the generated gcode file , otherwise SRM-20 will terminate and show an error
also it should be appended in the beginning of the gcode file , by adding 2 lines to user-gcode.h file , as shown below


settings/user_gcode.h

line 20

    FILE_BEGIN[ALL]    = "(Beginning of every file)\n"

added 2 lines after that to append the requested symbol to the beginning of the generated gcode file 

    "(Beginning of every file)\n"
     "%% \n"
     "G54\n";
           
 G54 is the coordinate system , used in the machine control panel "VPANEL for SRM-20" software
           
 Finally , the generated gcode file will have ".tap" extension
 to make it ".nc" file by default , just change the parameter as follow
 
 settings/pcb-gcode-options
 
 line 33
 
        string DEFAULT_EXTENSION = "tap";
 to
 
        string DEFAULT_EXTENSION = "nc";


I went through the other files
gcode-defaults.h and safe_options/
repeated the steps to make sure that a "restart to default" command won't change to values to something else
