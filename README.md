SFTPApplet
==========

This applet requires the jsch-0.1.36 (or later) Java Secure Channel library

Java applet for use in websites as a file uploader that allows multi-file select, and uses SFTP for secure access

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

