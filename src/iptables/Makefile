.ONESHELL:
SHELL = /bin/bash
.SHELLFLAGS += -e

MAIN_TARGET = $(IPTABLES)
DERIVED_TARGETS = libip4tc0_$(IPTABLES_VERSION_FULL)_amd64.deb \
		  libip6tc0_$(IPTABLES_VERSION_FULL)_amd64.deb \
		  libiptc0_$(IPTABLES_VERSION_FULL)_amd64.deb \
		  libxtables12_$(IPTABLES_VERSION_FULL)_amd64.deb

IPTABLES_URL = http://deb.debian.org/debian/pool/main/i/iptables

DSC_FILE = iptables_$(IPTABLES_VERSION_FULL).dsc
ORIG_FILE = iptables_$(IPTABLES_VERSION).orig.tar.bz2
DEBIAN_FILE = iptables_$(IPTABLES_VERSION_FULL).debian.tar.xz

DSC_FILE_URL = $(IPTABLES_URL)/$(DSC_FILE)
ORIG_FILE_URL = $(IPTABLES_URL)/$(ORIG_FILE)
DEBIAN_FILE_URL = $(IPTABLES_URL)/$(DEBIAN_FILE)

$(addprefix $(DEST)/, $(MAIN_TARGET)): $(DEST)/% :
	# Remove any stale files
	rm -rf ./iptables-$(IPTABLES_VERSION)

	# Get iptables release
	wget -NO "$(DSC_FILE)" $(DSC_FILE_URL)
	wget -NO "$(ORIG_FILE)" $(ORIG_FILE_URL)
	wget -NO "$(DEBIAN_FILE)" $(DEBIAN_FILE_URL)
	dpkg-source -x iptables_$(IPTABLES_VERSION_FULL).dsc

	pushd iptables-$(IPTABLES_VERSION)
	git init
	git add -f *
	git commit -m "unmodified iptables source"

	# Apply patches
	stg init
	stg import -s ../patch/series

	# Build source and Debian packages
	dpkg-buildpackage -rfakeroot -b -us -uc -j$(SONIC_CONFIG_MAKE_JOBS)
	popd

	# Move the newly-built .deb packages to the destination directory
	mv $(DERIVED_TARGETS) $* $(DEST)/

$(addprefix $(DEST)/, $(DERIVED_TARGETS)): $(DEST)/% : $(DEST)/$(MAIN_TARGET)
