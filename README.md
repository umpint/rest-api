# umpint.com REST API Documentation

umpint provides two endpoints signing files. For signing one file send a GET request to the `sign` endpoint 
and pass the hash of the file and signature as query parameters. For sending multiple signatures (up to 
100 at a time) send a POST to the `sign` endpoint and send a JSON object in the body of the request. Or to send
 up to 100,000 signatures use the asynchronous API at the `signasync` endpoint. Details are in links below.

The POST endpoint is a much more efficient process when signing more than a few files.

No authentication is required for any call - as your signature is proof you are the owner of the url.

### Signatures

Endpoints for uploading either a single hash/signature or multiple hash/signature pairs in a batch.

* [Sign Single Document](single.md) : `GET /api/v1/sign/:url?hash=:hash&sig=:signature`
* [Sign Multiple Documents](batch_sign.md) : `POST /api/v1/sign/:url`
* [Asynchronous Signing of Multiple Documents](async_api.md) : `POST /api/v1/signasync/:url`

where `:url` is the URL of the private key used to sign the file.
      `:hash` is the sha256 hash of the file in hex notation.
      `:signature` is the signature of the hash.
