# TLS Certificate Setup for HiveMQ

To generate a self-signed certificate for HiveMQ, run the following command:

```bash
# Generate a keystore with a self-signed certificate

keytool -genkeypair \
  -alias hivemq \
  -keyalg RSA \
  -keysize 2048 \
  -validity 365 \
  -keystore hivemq.jks \
  -storepass Mayker123! \
  -keypass Mayker123! \
  -dname "CN=mayker.com, OU=IoT, O=Mayker, L=Ghent, ST=Belgium, C=BE"

# In case you ran somewhere else, move the keystore to the cert directory
mv hivemq.jks /path/to/hivemq/cert/
```

> [!IMPORTANT] Make sure to update the keystore configuration in the HiveMQ config.xml file with the correct path and passwords.
