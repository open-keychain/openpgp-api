## *FIXED* OpenPGP API library

This is a fork of [OpenPGP API](http://github.com/open-keychain/openpgp-api)

It has been patched to address [this bug](https://github.com/open-keychain/open-keychain/issues/1504)

# Possible truncation of returned data (stream)

##When using the OpenPGP API library, data returned from the OpenKeychain app is through the ParcelFileDescriptor stream is likely to get truncated!

This is because the TransferThread doesn't necessarily read all available data back from the OpenKeychain service, because it is stopped immediately after the "execute" call has returned (it's stopped by  input.close();)

The obvious solution of simply waiting for (joining) the TransferThread  unfortunately does not work, because the TransferThread can not detect remote stream close/end  due to the way the pipe FileDescriptors are set up.

This is because the  FileDescriptors are "dup"ed when going through parcels, the input ParcelFileDescriptor never realises that the FileDescriptor at the service side has been closed. It works though if the return pipe gets set up on the service side.

