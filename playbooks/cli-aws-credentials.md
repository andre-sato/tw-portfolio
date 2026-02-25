In order to prevent security incidents, we are now using temporary AWS credentials so that in case they get leaked, the window which an attacker would be able to use them is much shorter since they expire in 12 hours. The following sections have instruction on how to enable them.

Prerequisites
⚠️ Important⚠️

Remove all AWS related environment variables (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_...) from all of these files (you probably don't have all of them): ~/.bash_profile, ~/.bashrc, ~/.zshrc, ~/.nurc.

Make sure you already have an AWS role. To check that, you should be able to open Okta --> "AWS". If you don't have one, please request here.

Configure Yubikey on Okta in Extra Verification and Security Key or Biometric Auth: https://company.okta.com/enduser/settings

Setup
Now configure your Okta credentials:



company aws credentials setup
If you use Google Authenticator, run:



company aws credentials setup --preferred-mfa-type 'token:software:totp'
Type in your Okta password and choose if you want to store it on the Keyring (Keychain for Mac users).



Okta Password for elliot.alderson:
Do you want to save this password in the keyring? (y/n) y
When requested, plug your yubikey and touch it to validate the MFA



Multi-factor Authentication required.
webauthn: webauthn selected
Challenge with security keys ...
Touch your authenticator device now...
or enter the 6 digits from Google Authenticator.

The temporary credentials for all AWS accounts you have access to will be saved on AWS credentials file.



Saving arn:aws:iam::923456789012:role/elliot.alderson-ecorp-role as ecorp-prod
Written profile ecorp-prod to /home/elliot/.aws/credentials
Saving arn:aws:iam::123456789012:role/elliot.alderson-sec-role as sec-prod
Written profile sec-prod to /home/elliot/.aws/credentials
After following these steps, your are ready to work with Nucli or AWS CLI as usual.

Refresh credentials
After 12 hours your AWS credentials should have expired and new ones need to be generated



company aws credentials refresh
It will authenticate on Okta again and write you AWS temporary credentials to the configuration file

Common issues
Python crash on MacOS
There is an issue with a version of the Python library cryptography, when gimme-aws-creds is executed it causes a crash on MacOS, the crash log will contain the following information:



Application Specific Information:
abort() called
Invalid dylib load. Clients should not load the unversioned libcrypto dylib as it does not have a stable ABI.
This issue can be solved by upgrading cryptography to its latest version:



pip3 install -U cryptography
Python crash Error initializing plugin EntryPoint('Windows (alt)', 'keyrings.alt.Windows', None, Distribution('keyrings.alt', '3.0'))
Seems like an issue in version 3.0 of the keyrings.alt package.



pip3 install -U keyrings.alt
Python crash TypeError: get_assertion() takes 2 positional arguments but 4 were given
You need to downgrade the fido2 package to version 0.7.3



pip3 install -Iv fido2==0.7.3
If you get this error when installing fido2, and the nu command still fails



ERROR: gimme-aws-creds 2.3.4 has requirement fido2<=0.9.0,>=0.8.0, but you'll have fido2 0.7.3 which is incompatible.
Maybe you have multiple versions installed. Try



pip3 uninstall fido2
Uninstalling fido2-0.8.1:
  Would remove:
    ~/.local/lib/python3.7/site-packages/fido2-0.8.1-py3.7.egg-info
    ~/.local/lib/python3.7/site-packages/fido2/*
Proceed (y/n)? y
  Successfully uninstalled fido2-0.8.1
pip crash /Users/name.surname/dev/nu/nucli/nucli.d/aws.d/.venv/bin/python3: No module named pip
Inside the pyvenv.cfg file change the false to true




home = /Users/john.doe/opt/miniconda3/bin
include-system-site-packages = false <--- change to true
version = 3.7.7
Python crash Keyring is skipped due to an exception: Failed to create the collection:
Disabling keyring solves the problem. 
