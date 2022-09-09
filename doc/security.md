## Data encryption
The AES encryption in CBC mode with key size = 265 bit is used.

During account's creation two keys are created:
1.  KEK (Key Encryption Key)
2.  MEK (Media Encryption Key)

The first takes SHA-256 hash of typed password as its base and the second is randomly generated.

As it can be understood from name, MEK is a key that encrypts data from database (logins, passwords and so on), 
and KEK encrypts MEK. It turns out two-level data encryption scheme with its big advantage (in common case) - 
duration of password changing does not depend on data amount that was encrypted with changing password. That is,
when using one-level encryption scheme the password's changing will require re-encryption of all the data that 
can take too long. But when using two-level scheme, all that need to be re-encrypted is MEK. Protected data
is not changed. 

Thus:
* KEK and MEK are created during account creation;
* MEK is randomly generated, and it encrypts all user data;
* KEK is generated with SHA-256 of user password as its base. KEK encrypts MEK;
* While password changing new KEK is created, and it's used for re-encryption of MEK. 
  The MEK itself and data stay unchanged.


## Account registration
While account registration two keys are created:
* KEK takes user's password as its base;
* MEK is randomly generated;

KEK encrypts MEK and encrypted MEK is saved in user's account.

The additional SHA-512 hash is created from user's password and saved into account as the standard. 
This hash is saved for authentication 


## User authentication
When authenticating a user the SHA-512 hash of password is calculated and compared with saved standard. 
If hashes are equal ***original user's password*** is converted into SHA-265 hash, which is KEK.