# Compilation of classbench

# Potential backup time
BACKUP_TIME=$(shell date --rfc-3339=seconds | tr ' ' '_')

all: compile

download:
	# Make backup of db_generator, if exists
	-[ -d db_generator ] && mv db_generator db_generator-$(BACKUP_TIME)

	# Download and unpack ClassBench
	wget -O- --no-check-certificate -q http://www.arl.wustl.edu/classbench/db_generator.tar.gz | tar -xvz

	# Make backup of parameter_files, if exists
	-[ -d parameter_files ] && mv parameter_files parameter_files-$(BACKUP_TIME)

	# Download and unpack ClassBench parameter files
	wget -O- --no-check-certificate -q http://www.arl.wustl.edu/classbench/parameter_files.tar.gz | tar -xvz

patch: download
	# Apply patches from ../patches
	git apply --directory=vendor/db_generator ../patches/improvements_ipv6.patch
	#git apply --directory=vendor/db_generator ../patches/ipv6.patch
	#git apply --directory=vendor/db_generator ../patches/improvements.patch

	# Patching PortList (extension of preallocated array is necessary)
	# Raise limit of L5 rules from 200 to 20000.
	sed -i 's/200/20000/' db_generator/PortList.h
	# solve bug for stack corruption in Ubuntu 17.04
	sed -i 's/comm\[[0-9]\]/comm\[7\]/g' db_generator/*.cc
        # Define return type for original ClassBench's main function
	sed -i 's/main/int main/' db_generator/db_generator.cc
	# Replace Null (i.e., integer constant equal to 0) with NULL (i.e., null pointer)
	sed -i 's/Null/NULL/' db_generator/FlagList.cc db_generator/custom_db.cc

compile: patch
	# Recurse to ClassBench compilation
	make -C db_generator -B db_generator

clean:
	rm -rf db_generator* parameter_files*
