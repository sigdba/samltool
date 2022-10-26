# samltool
SAML management and configuration can be tedious and repetitive.
With more clients transitioning from CAS to SAML, I felt like it was time to finish up a tool I had started a long time ago.

I created a bash script called 'samltool.sh' and its main purpose (for now) is to prompt the user for the information that it 
needs to generate a SAML signing keypair and a Service Provider metadata file.

In the example below, we're going to use the script to generate SP metadata for the SIG General SSB application.
The base URL for the application is: https://banner.ban.sig.sh/BannerGeneralSsb

[ladmin@weathertop saml]$ ls -l
total 8
drwxr-xr-x. 2 ladmin luminis   29 Oct 20 05:20 rockhurst
-rwxr-xr-x. 1 ladmin luminis 2632 Oct 25 15:53 samltool.sh
-rw-r--r--. 1 ladmin luminis 1640 Oct 20 18:03 sp-metadata-template.xml
[ladmin@weathertop saml]$ ./samltool.sh
--------------------------------------------
█▀ █ █▀▀   █▀ ▄▀█ █▀▄▀█ █    ▀█▀ █▀█ █▀█ █
▄█ █ █▄█   ▄█ █▀█ █ ▀ █ █▄▄   █  █▄█ █▄█ █▄▄
--------------------------------------------

version 1.0

[*] Collecting environment information
- Enter hostname: banner.ban.sig.sh
- Enter context path: BannerGeneralSsb
- Enter entity ID (suggested value='https://banner.ban.sig.sh/BannerGeneralSsb'): https://banner.ban.sig.sh/BannerGeneralSsb
- Enter ID: SIG General SSB
[*] Creating working directory (working-directory-20221025-155508)
[*] Copying SP metadata template
[*] Do you need to generate a certificate? (Y/N) Y
[*] Generating keystore...
- Enter cert data: CN=saml-banner-test.ban.sig.sh,OU=IT,O=SIG,L=San Diego,ST=CA,C=US
Enter keystore password:
Re-enter new password:
Enter key password for <samlsigning>
        (RETURN if same as keystore password):
[*] Exporting public certificate...
Enter keystore password:
Certificate stored in file <saml-signing.crt>
- Cleaning up and formatting cert
dos2unix: converting file saml-signing-temp.crt to Unix format ...
[*] Updating SP metadata...
- Updating entity id
- Updating id
- Updating certificate
- Updating host
- Updating context
[*] Done.
[ladmin@weathertop saml]$

[ladmin@weathertop saml]$ ls -l working-directory-20221025-155508/
total 24
-rw-r--r--. 1 ladmin luminis 2254 Oct 25 15:55 samlKeystore.jks
-rw-r--r--. 1 ladmin luminis 1287 Oct 25 15:55 saml-signing.crt
-rw-r--r--. 1 ladmin luminis 1196 Oct 25 15:55 saml-signing-flat.crt
-rw-r--r--. 1 ladmin luminis 1215 Oct 25 15:55 saml-signing-temp.crt
-rw-r--r--. 1 ladmin luminis 4107 Oct 25 15:55 sp-metadata.xml
[ladmin@weathertop saml]$

[ladmin@weathertop saml]$ vi working-directory-20221025-155508/sp-metadata.xml
[ladmin@weathertop saml]$

What if you already have a signing certificate to use?
/A keypair may have been provided, or this might not be the first application in the environment and a keypair already exists.
Enter 'N' or 'n' at the 'Do you need to generate a certificate?' prompt and the script will then prompt for the keystore details.

In the example below, we have an existing keystore named 'exampleKeystore.jks' and it contains a keypair under the 'samlsigning' alias.

[ladmin@weathertop saml]$ keytool -genkeypair -keyalg RSA -alias samlsigning -dname "CN=saml-banner-test.ban.sig.sh,OU=IT,O=SIG,L=San Diego,ST=CA,C=US" -validity 1825 -keysize 2048 -sigalg SHA256withRSA -keystore exampleKeystore.jks
Enter keystore password:
Re-enter new password:
Enter key password for <samlsigning>
        (RETURN if same as keystore password):
[ladmin@weathertop saml]$

The 'samltool.sh' script is run for the 'Student Registration SSB' application this time.
After entering 'N' or 'n' at the prompt to generate a certificate, the script will prompt the user for the necessary keystore details.

[ladmin@weathertop saml]$ ./samltool.sh
--------------------------------------------
█▀ █ █▀▀   █▀ ▄▀█ █▀▄▀█ █    ▀█▀ █▀█ █▀█ █
▄█ █ █▄█   ▄█ █▀█ █ ▀ █ █▄▄   █  █▄█ █▄█ █▄▄
--------------------------------------------
version 1.0

[*] Collecting environment information
- Enter hostname: banner.ban.sig.sh
- Enter context path: StudentRegistrationSsb
- Enter entity ID (suggested value='https://banner.ban.sig.sh/StudentRegistrationSsb'): https://banner.ban.sig.sh/StudentRegistrationSsb
- Enter ID: SIG Student Registration
[*] Creating working directory (working-directory-20221025-155924)
[*] Copying SP metadata template
[*] Do you need to generate a certificate? (Y/N) N
[*] Prompting for keystore details...
- Enter keystore path: /home/ladmin/saml/exampleKeystore.jks
- Enter key alias: samlsigning
[*] Exporting public certificate...
Enter keystore password:
Certificate stored in file <saml-signing.crt>
- Cleaning up and formatting cert
dos2unix: converting file saml-signing-temp.crt to Unix format ...
[*] Updating SP metadata...
- Updating entity id
- Updating id
- Updating certificate
- Updating host
- Updating context
[*] Done.
[ladmin@weathertop saml]$


[ladmin@weathertop saml]$ ls -l working-directory-20221025-155924/
total 20
-rw-r--r--. 1 ladmin luminis 1287 Oct 25 16:00 saml-signing.crt
-rw-r--r--. 1 ladmin luminis 1196 Oct 25 16:00 saml-signing-flat.crt
-rw-r--r--. 1 ladmin luminis 1215 Oct 25 16:00 saml-signing-temp.crt
-rw-r--r--. 1 ladmin luminis 4140 Oct 25 16:00 sp-metadata.xml
[ladmin@weathertop saml]$
