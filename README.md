# FBOSS Board Support Package

## Building

### Build kernel modules directly

```bash
make -C kmods BUILD_KERNEL=$(uname -r)
```

### Build RPM package

First, create the source RPM:

```bash
SPEC=rpmbuild/fboss_bsp_kmods.spec
NAME=$(grep '^Name:' "$SPEC" | awk '{print $2}')
VERSION=$(grep '^Version:' "$SPEC" | awk '{print $2}')

mkdir -p ~/rpmbuild/{SOURCES,SPECS,SRPMS}
tar czf ~/rpmbuild/SOURCES/${NAME}-${VERSION}.tar.gz \
    --transform "s,^,${NAME}-${VERSION}/," \
    kmods/ rpmbuild/

rpmbuild -bs "$SPEC"
```

Then, build the binary RPM from the source RPM:

```bash
rpmbuild --rebuild ~/rpmbuild/SRPMS/${NAME}-${VERSION}-*.src.rpm
```

This requires `kernel-devel` to be installed for the target kernel version.
To build against a specific kernel version:

```bash
rpmbuild --rebuild ~/rpmbuild/SRPMS/${NAME}-${VERSION}-*.src.rpm \
    --define "rpm_kernel_version $(uname -r)"
```

The resulting RPM will be in `~/rpmbuild/RPMS/`.

## License
fboss.bsp.kmods is GPLv2.0 licensed, as found in the LICENSE file.
