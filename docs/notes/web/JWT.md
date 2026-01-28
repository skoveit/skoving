- Now think out _of the box_ 

JSON Web Token used in auth. it could be:-
#### JSON Web Signature
it is readable cz it encode, And conatain 3 parts:-
- **Header**: Specifies the encryption algorithms.
- **Payload**: Contains the Data.
- **Signature**: Verifies the integrity of the data.

#### JSON Web Encryption
it isn't readable cz it enctypt, And conatain 5 parts:-
- **Protected Header**: Specifies the encryption algorithms.
- **Encrypted Key**: Contains the key used to encrypt the content.
- **Initialization Vector (IV)**: Ensures unique encryption.
- **Ciphertext**: The encrypted content or payload.
- **Authentication Tag**: Verifies the integrity of the data.

There is others                                                                                                                                    :)

# Hacking 
- no checks like using jwt.decode() direct 
- can use alg:none or alg:NonE
- the key could be cracked?
- there is some header's parameters can be accepted by the server like
	- `jwk` (JSON Web Key) - Provides an embedded JSON object representing the key.
		- Try to encrypt the token using rsa private key and pass the public key here
	
	- `jku` (JSON Web Key Set URL) - Provides a URL from which servers can fetch a set of keys containing the correct key.
		- the same thing you can make extended file 'keys' contain rsa public key then encode it using the private key
	
	- `kid` (Key ID) - Provides an ID that servers can use to identify the correct key.
		- Try path traversal cz some time it grep from system file and could be stored so try sqli 
	
- ## JWT algorithm confusion
	
	```javascript
	function verify(token, secretOrPublicKey){
		algorithm = token.getAlgHeader();
		if(algorithm == "RS256"){}
		else if (algorithm == "HS256"){}
	}
	
	publicKey = <public-key-of-server>;
	token = request.getCookie("session");
	verify(token, publicKey);
	```
	- you can try to use the publick key as secret key and switch alg from `RS256` to `HS256` 
	- if you do not have a public key you can try to forge it using tools like  `jwt_forgery.py` or `docker run --rm -it portswigger/sig2n <token1> <token2>` 