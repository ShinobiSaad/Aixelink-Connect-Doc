# Installation

Prerequisites

 - You need to have installer of Translator service ie: <mark>PH.Device.Translator.Server.exe</mark>
 - You need to have <mark>Translator.bak</mark> file
 - "Precept Connect" web application folder either cloned for GitHub or copied from owner

Process

<b>1) Translator Service Installation</b>

 -  Start the installation.exe with Administrator privileges 
 -  SQL Server Instance will be your connection name (That you used to created db in SSMS)
 -  SQL Server sa password is "dt2554DT"
  
after that

- HL7 MLLP IP = 127.0.0.1
- HL7 MLLP Port = 83

And then click install

- To check whether it is installed properly Press the Win + R keys on your keyboard, to open the Run window. Then, type "services. msc" and hit Enter or press OK.
  Then search for <b>Precept Translator</b>

<b>2) Translator DB Installation</b>

 - Locate <mark>PreceptService.db</mark> file inside the Precept Health\Translator folder and open it on the SQLiteStudio
 - Use password "pr3c3pt2554" and OK
 - Select the PreceptService DB and click "Data"
 - On the "Value" of some "Section" there should be a property named "server", change that to you connection name that you have in SSMS
 - After changing all occurences hit Commit button on the top (a green tick)
  And now you have SQLiteDB for the Translator

<b>3) Restoring Precept Connect Database</b>

Open the Microsoft SQL Server Studio
  - Go to Connection
  - Select Databases
  - Right Click on it and select "Restore Database..."
  - Locate "Translator.bak"
  - restore the database
  So now you have the DB for Precept connect

<b>4) Running Precept Connect in Production</b>

Open Precept Connect folder (Web app)
  - Run cmd on the folder or open terminal
    - npm i (to install all the dependencies)
    - npm run build (to create dist folder for production)
    - build.bat no (build.bat located in the root directory. Use build.bat yes when needed to import <mark>Forms</mark> or changes on it)
 
 It will run the scripts on the build.bat file and start the development server running on <url>http://localhost:83</url>

<b>4) Running Precept Connect in Development</b>

Open Precept Connect folder (Web app)
  - Run cmd on the folder or open terminal
    - npm run serve
  
  Now the Precept Connect will run at <url>http://localhost:3000</url> server

<i>Note: Some forms are rendered from path /dist so you have to change the path in order to see them in <url>http://localhost:3000</url></i>
