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
wget https://archive.apache.org/dist/activemq/5.16.0/apache-activemq-5.16.0-bin.tar.gz
-O rpm/SOURCES/apache-activemq-5.16.0-bin.tar.gz
```

### Create the RPM spec file with out JAVA:

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
Source0:        activemq-5.16.0.tar.gz
Requires:       gzip
License:        MIT

%description
An RPM for installing activemq v5.16.0 without affecting host PATH. 

%prep




%build


%install
[ -d %{buildroot}/ActiveMQ ] && rm -rf %{buildroot}/ActiveMQ
mkdir %{buildroot}/ActiveMQ/
cp $RPM_SOURCE_DIR/activemq-5.16.0.tar.gz %{buildroot}/ActiveMQ/

%post
tar xvf /ActiveMQ/activemq-5.16.0.tar.gz -C /ActiveMQ/
/ActiveMQ/apache-activemq-5.16.0/bin/activemq start

%preun
/ActiveMQ/apache-activemq-5.16.0/bin/activemq stop

%files
/ActiveMQ/

```
### With JAVA

add java-sdk to rpmbuild/SOURCE folder:
```bash
wget https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_linux-x64_bin.tar.gz
```

config AMQ to work with dedicated java sdk:

```bash
tar -xzf rpmbuild/SOURCE/activemq-5.16.0.tar.gz -C /tmp/activemq-5.16.0
vim /tmp/activemq-5.16.0/bin/env

// Change
// #JAVA_HOME=''
// to
// JAVA_HOME="ActiveMQ/openjdk-11.0.2_linux-x64_bin/bin/java" 
tar -czf /tmp/activemq-5.16.0.tar.gz /tmp/activemq-5.16.0


```

```vim
Name:           activemq_ojdk11 
Version:        1
Release:        1%{?dist}
Summary:        An RPM installing standalone activeMQ      
Source0:        openjdk-11.0.2_linux-x64_bin.tar.gz
Source1:        activemq-5.16.0.tar.gz
Requires:       gzip
License:        GPL

%description
An RPM for installing activemq v5.16.0 which also installs open jdk v11.0.2 without affecting host PATH. 

%prep




%build


%install
[ -d %{buildroot}/ActiveMQ ] && rm -rf %{buildroot}/ActiveMQ
mkdir %{buildroot}/ActiveMQ/
cp $RPM_SOURCE_DIR/openjdk-11.0.2_linux-x64_bin.tar.gz %{buildroot}/ActiveMQ/
cp $RPM_SOURCE_DIR/activemq-5.16.0.tar.gz %{buildroot}/ActiveMQ/

%post
pwd
tar xvf /ActiveMQ/openjdk-11.0.2_linux-x64_bin.tar.gz -C /ActiveMQ/
tar xvf /ActiveMQ/activemq-5.16.0.tar.gz -C /ActiveMQ/
/ActiveMQ/apache-activemq-5.16.3/bin/activemq start

%preun
/ActiveMQ/apache-activemq-5.16.0/bin/activemq stop

%files
/ActiveMQ/



%changelog
* Wed Dec  1 2021 root
- 
```

### Check the spec file for errors:

```bash
rpmlint ~/rpmbuild/SPECS/activemq.spec
```
### Build the RPM package:
```bash
rpmbuild -bb ~/rpmbuild/SPECS/activemq.spec
```

## The RPM package is created at ~/rpmbuild/RPMS/x86_64/activemq-5.16.0-1.x86_64.rpm
