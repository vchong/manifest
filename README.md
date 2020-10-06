OPTEE + widevine using QEMU.

# Repo manifest for OP-TEE development
This branch in git contains repo manifests to be able to clone all source code needed to
be able to setup a full OP-TEE developer build with widevine.

OEMCrypto is a component tied with a NDA from Google (it's called Widevine Master License Agreement).
Due to this source code etc written cannot be shared in a public repository. Instead we use an internal
git only accessible to members that we (Linaro) know have signed the NDA with Google.

### Get the Source
Pre-condition, install the pre-reqs mentioned at the official OP-TEE docs:
https://optee.readthedocs.io/building/prerequisites.html

When done, continue here.

```bash
$ mkdir optee-ww && cd optee-ww
$ repo init -u https://github.com/ruchi393/manifest.git -b oemcrypto-v15 -m qemu_v8_ww.xml 
$ repo sync -j4 --no-clone-bundle
```

### Compile
The initial build takes quite some time, have patience!

```bash
$ cd <optee-ww>/build
$ make toolchains -j2
$ make -j`nproc`
```

### Run
```bash
$ cd <optee-ww>/build
$ make -j`nproc` run
```

#### Shell 1 - QEMU:
```
(qemu) c
```

#### Shell 2 - QEMU:NW
Here we will login, run alias `setup_oemcrypto`
```
buildroot login: root
# alias setup_oemcrypto='mkdir -p /host && mount -t 9p -o trans=virtio host /host && ln -sf /host/optee-widevine-ref/out/ta/oemcrypto/57696465-7669-6e65-7631-354f50544545.ta /lib/optee_armtz/ && ln -sf /host/optee-widevine-ref/out/lib/liboemcrypto/liboemcrypto.so /usr/lib/liboemcrypto.so && ln -sf /host/optee-widevine-ref/out/gtest/gtest/oemcrypto_v15_test /usr/bin/oemcrypto_v15_test && ln -sf /host/optee-widevine-ref/ce_cdm/out/aarch64/Release/widevine_ce_cdm_unittest /usr/bin/widevine_ce_cdm_unittest'
# setup_oemcrypto
# widevine_ce_cdm_unittest
```

#### Test Results
The tests takes quite some time to run, have patience!

You will see the logs below as the test start running.

```
Default Server: https://proxy.uat.widevine.com/proxy
Default KeyID: 000000427073736800000000edef8ba979d64acea3c827dcd51d21ed0000002208011a0d7769646576696e655f74657374220f73747265616d696e675f636c697031

provisioning_method = OEMCrypto_Keybox
E/OEC: teec_invoke:71: TEEC_InvokeCommand (OPTEE_WV_GETKEYDATA) failed with code 0xffff0000 origin 0x4 oec_result 7
uses_keybox = true.
loads_certificate = true.
uses_certificate = true.
generic_crypto = true.
api_version = 15.
usage_table = true.
cast_receiver = false.
resource_rating = 2, security level L1.
Decrypt hashes will be tested.
LOAD_TEST_KEYBOX: Call LoadTestKeybox before deriving keys.
SecurityLevel is Level 1 (L1)
Note: Google Test filter = *-*ForceKeybox*:*CastReceiver*:*Prov30*:*RSAKey3072*:*Performance*
[==========] Running 1082 tests from 77 test cases.
[----------] Global test environment set-up.
[----------] 20 tests from OEMCryptoClientTest
...
...
```

When the tests finish running, a Test Report is printed at the end. You can expect to see the following log
```
[----------] Global test environment tear-down
[==========] 1082 tests from 77 test cases ran. (1488475 ms total)
[  PASSED  ] 1081 tests.
[  FAILED  ] 1 test, listed below:
[  FAILED  ] OEMCryptoClientTest.ClearCopyTestAPI10

 1 FAILED TEST
  YOU HAVE 4 DISABLED TESTS
```

# Official OP-TEE documentation
All other official OP-TEE documentation has moved to
http://optee.readthedocs.io. The information that used to be here in this git
can be found under [manifest].

// OP-TEE core maintainers

[manifests]: https://optee.readthedocs.io/en/latest/building/gits/build.html#manifests
