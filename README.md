# RPM

The work flow to create RPM package and install the Apache ActiveMQ open source message broker.

## RPM tools installation

```bash
sudo dnf install -y rpmdevtools rpmlint
```
1. rpmdevtools: core tools for RPM
2. rpmlint: check the RPM spec file for erroes

### Create rpm folder and sub folders at home directory:

```bash
rpmdev-setuptree
tree
rpmbuild/
    ├── BUILD
    ├── RPMS
    ├── SOURCES
    ├── SPECS
    └── SRPMS
```

### Download the AMQ tar.gz to rpmbuild/SOURCES folder:

```bash
wget https://www.apache.org/dyn/closer.cgi?filename=/activemq/5.16.3/apache-activemq-5.16.3-bin.tar.gz&action=download -O rpm/SOURCES/apache-activemq-5.16.3-bin.tar.gz
```

### Create the RPM spec file:

```bash
rpmdev-newspec activemq.spec

vi rpmbuild/SPECS/activemq.spec
```

and paste there:

```vim
Name:           activemq
Version:        1
Release:        1%{?dist}
Summary:        An RPM installing standalone activeMQ
Source0:        activemq-5.16.3.tar.gz
Requires:       gzip
License:        MIT

%description
An RPM for installing activemq v5.16.3 without affecting host PATH. 

%prep




%build


%install
[ -d %{buildroot}/ActiveMQ ] && rm -rf %{buildroot}/ActiveMQ
mkdir %{buildroot}/ActiveMQ/
cp $RPM_SOURCE_DIR/activemq-5.16.3.tar.gz %{buildroot}/ActiveMQ/

%post
tar xvf /ActiveMQ/activemq-5.16.3.tar.gz -C /ActiveMQ/
/ActiveMQ/apache-activemq-5.16.3/bin/activemq start

%preun
/ActiveMQ/apache-activemq-5.16.3/bin/activemq stop

%files
/ActiveMQ/

```

### Check the spec file for errors:

```bash
rpmlint ~/rpmbuild/SPECS/activemq.spec
```
### Build the RPM package:
```bash
rpmbuild -bb ~/rpmbuild/SPECS/activemq.spec
```

## The RPM package is created at ~/rpmbuild/RPMS/x86_64/activemq-5.16.3-1.x86_64.rpm
