SFTPApplet
==========

This applet requires the jsch-0.1.36 (or later) Java Secure Channel library, 
as well as plugin.jar (which can be found in the jdk)

Java applet for use in websites as a file uploader that allows multi-file select, and uses SFTP for secure access.

The plugin will can be configured to use specific file filters (through a call to a webservice) and to validate 
the files before sending is allowed. The webservices will always be on the same server that the applets codebase
is set to (and it is expected that it will be an HTTPS call). Additionally, before sending, it will call the webservice
to retrieve the SFTP username and passowrd (thus these never have to be code into the applet). At any of these stages, 
you can add authentication on the webservice side.

The applet is programmable via JavaScript. There are methods available to show the user's file select dialog 
(which is a Swing component), get the list of selected files, and to send the files. Callbacks are configurable so that
the applet can tell the JavaScript the status of a file transfer (success or fail) and the percentage of the file(s) sent.

The callbacks can be set in the applet tag via the following parameters:
FilePercentCallBack - Called periodically with the percentage transferred for the current file.
PercentCallBack - Called periodically with the overall percentage transferred
StatusCallback - Called periodically with the status of the transfer batch
FTPCompleteCallback - Called when each file is done with a status for that file
FileCompleteCallback - Called when the entire batch is completed with the overall status
FileSelectCallback- Called when the use clicks OK on the file selection dialog, and passed the list of files selected (as a comma delimited list)

Additionally, the methods exposed are:
setPercentCallBack - Sets the Percent Callback function name.
setFilePercentCallBack - Sets the File Percent Callback function name.
setStatusCallBack - Sets the status Callback function name.
setFileCompleteCallBack - Sets the File Complete Callback function name.
setFtpCompleteCallBack - Sets the FTP Complete Callback function name.
setFileSelectCallBack - Sets the File selected Callback function name.

getAccessCode - return the current authentication nonce code.
setAccessCode - sets the authentication nonce code.
sendFiles( String accessCode,  String fileList) - send a list of files (comma delimted list), and use the specified nonce code. The file list must be a subset of those selected by the user the last time the selection dialog was opened.
sendFiles( String accessCode ) - send the last user selected list of files.
sendFiles( ) - Send the last user selected list of files, and use the internal auth nonce.
getFiles( boolean rememberLastDir ) - Show the use file selection dialog. Optionally, remember the last directory selected.
getSingleFile( boolean rememberLastDir ) - Same as getFiles, but allows the user to select only one file.

The applet makes use of the ftpauthserver.php to authorize files, get the list of file filters, and to indicate when a file has been transferred. 


=== Building ===

This applet requires the jsch-0.1.36 (or later) Java Secure Channel library, 
as well as plugin.jar (which can be found in the jdk) these should both be in your build path. 

First, you must complie the code and create a JAR file (called SFTPApplet.jar)

Make sure to include the jsch-0.1.36.jar file within SFTPApplet.jar

Then you must sign the jar file (so that it may access the local computer, and make webservice calls).

Creating a test certificate and signing your jar just involves three simple commands. 
The following shows the commands needed to firstly create your test certificate and add 
the certificate (with an alias of me) to a keystore (named myKeyStore).

keytool -genkey -keystore myKeyStore -alias me
keytool -selfcert -keystore myKeyStore -alias me

The above only needs to be done once.
You can then use the keystore to sign your jar using the following command:

jarsigner -keystore myKeyStore jarfile.jar me

=== Using ===

Use the following applet tag to include on a page:

<APPLET code="SFTPApplet" id="ftpappl" width="1" height="1" archive="SFTPApplet.jar,jsch-0.1.36.jar" codebase='https://some.secure.server.com/' MAYSCRIPT>
  <PARAM name="FilePercentCallBack" value="setFilePercent">
  <PARAM name="PercentCallback" value="setPercent">
  <PARAM name="StatusCallback" value="setStatus">
  <PARAM name="FileCompleteCallback" value="finished">
  <PARAM name="FTPCompleteCallback" value="allfinished">
  <PARAM name="FileSelectCallback" value="setFilesSelected">
Java Applet Support Required
</APPLET>  

If the applet loads successfully, you can make calls to it through JavaScript:

document.ftpappl.getFiles(true);

function setFilesSelected( inFileList ) {
  var returnCode = document.ftpappl.sendFiles(currSecurityKey, inFileList);
  currSecurityKey = document.ftpappl.getAccessCode();
}
