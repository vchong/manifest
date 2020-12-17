OPTEE + PR using QEMU.

# Repo manifest for OP-TEE development
This branch in git contains repo manifests to be able to clone all source code needed to
be able to setup a full OP-TEE developer build with PR.

PR is a component tied with a NDA from MS.
Due to this source code etc written cannot be shared in a public repository. Instead we use an internal
git only accessible to members that we (Linaro) know have signed the NDA with MS.

### Get the Source
Pre-condition, install the pre-reqs mentioned at the official OP-TEE docs:
https://optee.readthedocs.io/building/prerequisites.html

When done, continue here.

```bash
$ mkdir optee-pr && cd optee-pr
$ repo init -u https://github.com/vchong/manifest.git -b pr44 -m qemu_v8_pr.xml

# Change the username for the lhg remote in qemu_v8_pr.xml to your own first.last name!

$ repo sync -j`nproc` --no-clone-bundle
```

### Compile
The initial build takes quite some time, have patience!

```bash
$ cd optee-pr/build
$ make toolchains -j`nproc`
$ make -j`nproc`

# To build just the PRPK
$ make pr

# To build just the PR TA
$ make pr-ta

# To clean PR targets
$ make pr-ta-clean pr-clean
```

**NOTE**
- Parallel builds (`make -j#`) for the `pr*` targets are **NOT** stable. In case of errors, use `make -j1` or just `make`.
- `pr-ta` depends on `pr`.

### Run
```bash
$ cd optee-pr/build

# For the 1st run ONLY
$ make -j`nproc` QEMU_VIRTFS_ENABLE=y QEMU_USERNET_ENABLE=y CFG_TA_ASLR=n run

# For subsequent runs, to launch QEMU only without building
$ make -j`nproc` QEMU_VIRTFS_ENABLE=y QEMU_USERNET_ENABLE=y CFG_TA_ASLR=n run-only
```

#### Shell 1 - QEMU:
```
(qemu) c
```

#### Shell 2 - QEMU:NW
Here we will login, run alias `setup_pr`
```
buildroot login: root
# alias setup_pr='mkdir -p /host && mount -t 9p -o trans=virtio host /host && ln -sf /host/optee-playready/out/ta/82dbae9c-9ce0-47e0-a1cb-4048cfdb84aa.ta /lib/optee_armtz/ && ln -sf /host/optee-playready/out/../../playready/obj/pritee_test_utility.exe /usr/bin/ && ln -sf /host/optee-playready/out/../../playready/obj/playready_test.exe /usr/bin/'
# setup_pr
# playready_test.exe
# pritee_test_utility.exe -drmpath:/host/playready/Samples
```

#### Test Results
The tests takes quite some time to run, have patience!

You will see the logs below as the test start running.

```
--------------------------------------------------------------------------------
Test Area: Provisioning

TEST_LocalProvisioning
Initialize TEE context...
INIT DONE
	RESULT=PASS

TEST_RemoteProvisioning
Initialize TEE context...
INIT DONE

 Incompatible provisioning type, skipping test.
	RESULT=PASS


--------------------------------------------------------------------------------
Test Area: Domains

TEST_DomainJoin_LPROV
Initialize TEE context...
INIT DONE
	RESULT=PASS

TEST_DomainJoin_RPROV
Initialize TEE context...
INIT DONE
	RESULT=PASS


--------------------------------------------------------------------------------
Test Area: Licenses

TEST_License_DeviceBound_Simple_LPROV
Initialize TEE context...
INIT DONE
	OEMManufacturerName "
	OEMModelName "
	OEMModelNumber "
	OEMVersion [0].[1].[2].[3] "1.0.0.0"
```

# Official OP-TEE documentation
All other official OP-TEE documentation has moved to
http://optee.readthedocs.io. The information that used to be here in this git
can be found under [manifest].

// OP-TEE core maintainers

[manifests]: https://optee.readthedocs.io/en/latest/building/gits/build.html#manifests
