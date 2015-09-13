<!-- 
vim:ft=markdown:et:ts=2:sw=2
-->

Two parts
=========
- nginx-sso auth endpoint
- login middleware / function
 - https://justinas.org/writing-http-middleware-in-go/
 - function to create the cookie based on some payload (hash + ecdsa + setting the cookie)
 - user-defined function to get the username / group / payload (in general)

Problems
========
- Default values for JSON config parse
- Logging withFields (e.g. during auth with IP / URL)
- Put more common methods into ssocookie (print request, get headers)

- Unify method naming- and argument convention

- Figure out good integration of login code into existing systems

- Maybe implement generic JSON payload
  - http://devel.io/2013/08/19/go-handling-arbitrary-json/
  - On top of that: Have generic functions to verify the payload (ACL)
  - Will need generic mapping of attribute to return HTTP header as well

- Explore whether we can specify HTTP Host during login and set the cookie for that domain
 - This way one login-controller can service multiple domains

Others
======
- nginx-sso: https://www.timmclean.net/2015/03/31/jwt-algorithm-confusion.html
- nginx/auth/cookie: http://wiki.shopware.com/SSO-with-Nginx-auth_request-module_detail_1811.html
- https://developers.shopware.com/blog/2015/03/02/sso-with-nginx-authrequest-module/
 - token passed to backend
- Apache: authtkt
- HN: https://news.ycombinator.com/item?id=7641148
- Old PubCookie module for nginx: http://www.vitki.net/book/page/pubcookie-module-nginx

- Also document that this is a very basic solution
 - Next step could be to put a better-performing session store in which does not actually have to verify the sig each time only hash the cookie or set another cookie
 - Maybe even set a cookie and then have nginx verify the cookie from there on
 - Better read up on nginx session / auth management, performance, best practices
 - Login could be done using something like oauth

- https://elithrar.github.io/article/custom-handlers-avoiding-globals/ for handling context in a golang web app
- libsodium

ECC Resources
=============

https://stackoverflow.com/questions/7478821/how-can-i-convert-a-ecdsa-curve-specification-from-the-sec2-form-into-the-form-n
- secp256r1 is the same as NIST P-256
- https://tools.ietf.org/html/rfc4492#appendix-A

| Koblitz |  ECC  |  DH/DSA/RSA
|   163   |  192  |     1024
|   283   |  256  |     3072
|   409   |  384  |     7680
|   571   |  521  |    15360

prime256v1 best candidate (RSA 3072 bit equivalent)
http://wiki.openssl.org/index.php/Command_Line_Elliptic_Curve_Operations

openssl ecparam -list_curves
openssl ecparam -list_curves|grep 192
openssl ecparam -list_curves|grep 256
openssl ecparam -name prim256v1 -out prime256v1.pem
openssl ecparam -name prime256v1 -out prime256v1.pem
openssl ecparam -in prime256v1.pem -genkey -noout -out prime256v1-key.pem

https://www.socketloop.com/tutorials/golang-example-for-ecdsa-elliptic-curve-digital-signature-algorithm-functions