# Encryption

Authenticated Encryption refers to a form of encryption which simultaneously assures the confidentiality and authenticity of data.

The chosen approach was to encrypt-then-mac which produces a MAC based on the resulting ciphertext. The encryption uses AES-256-CBC with a random Initialization Vector (IV) and authentication uses HMAC-SHA256. The encrypted payloads are serialized in the following order: iv, publicKey, mac and cipherText.

To derive the encryption and authentication keys it uses a SHA512 hash of the shared key using the first 32bytes for encryption and the last 32 bytes for authentication