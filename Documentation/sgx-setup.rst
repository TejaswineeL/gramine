Set up the host environment
===========================

.. note ::
   Currently, Gramine executes on two environments:
   Linux-direct (execution on the host Linux OS) and
   Linux-SGX (execution inside an SGX enclave). This page
   guides to set up the environment for Linux-SGX.
   Linux-direct doesn't require any specific environment set up.

Gramine with SGX support requires several features from your system:

- Intel SGX must be enabled in BIOS.
- Linux kernel version must be at least 5.11 (starting from this version, Linux
  has the FSGSBASE feature and the SGX driver required by Gramine).
- The Intel PSW must be installed.
- The Intel DCAP must be installed if DCAP-based attestation will be used.

Check for SGX compatibility
---------------------------

We supply a tool, :doc:`manpages/is-sgx-available` that checks the environment
for SGX compatibility. Use this tool to check your hardware and system. It is
installed together with the Gramine package. For a quick check you can use the
below command::

       is-sgx-available

Dependencies for SGX
^^^^^^^^^^^^^^^^^^^^

The build of Gramine with SGX support requires the corresponding SGX software
infrastructure to be installed on the system. We require Linux kernel with SGX
driver built in (``CONFIG_X86_SGX=y``, which is the case for most of available
distribution kernels), which is available since version 5.11 (and also as
backported patches to older kernels in certain distros). Note this requires CPU
with :term:`FLC`.

Kernel version can be checked using the following command::

       uname -r

If your current kernel version is lower than 5.11, then you have two options:

- Update the Linux kernel to at least 5.11 in your OS distro. If you use Ubuntu,
  you can follow `this tutorial
  <https://itsfoss.com/upgrade-linux-kernel-ubuntu/>`__.

- Install out-of-tree driver and use our provided patches to the Linux kernel
  version 5.4. See section :ref:`legacy-kernel-and-hardware` for the exact
  steps.

1. Required packages
""""""""""""""""""""
Run the following commands on Ubuntu to install SGX-related dependencies::

    sudo apt-get install -y libprotobuf-c-dev protobuf-c-compiler \
        protobuf-compiler python3-cryptography python3-pip python3-protobuf

2. Install Intel SGX SDK/PSW
""""""""""""""""""""""""""""

Follow the installation instructions from the latest version of "Intel SGX
Software Installation Guide":

- https://download.01.org/intel-sgx/latest/dcap-latest/linux/docs/Intel_SGX_SW_Installation_Guide_for_Linux.pdf

In general, various documentation for Intel SGX SDK/PSW can be found here:

- https://download.01.org/intel-sgx/latest/dcap-latest/linux/docs/
- https://download.01.org/intel-sgx/latest/linux-latest/docs/

Additional information, package descriptions, etc. can be found in the official
"Intel SGX for Linux" GitHub repo:

- https://github.com/intel/linux-sgx

3. Install dependencies for DCAP
""""""""""""""""""""""""""""""""

If you plan on enabling ``-Ddcap`` option (see :term:`DCAP`), you need to install
``libsgx-dcap-quote-verify`` package(and its development counterpart):::

   # Below commands work on Ubuntu 22.04 LTS and 20.04 LTS
   sudo curl -fsSLo /usr/share/keyrings/intel-sgx-deb.asc https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key
   echo "deb [arch=amd64 signed-by=/usr/share/keyrings/intel-sgx-deb.asc] https://download.01.org/intel-sgx/sgx_repo/ubuntu $(lsb_release -sc) main" \
   | sudo tee /etc/apt/sources.list.d/intel-sgx.list

   sudo apt-get update
   # Below command automatically installs libsgx-dcap-quote-verify as an additional package along with its dev counterpart.
   sudo apt-get install libsgx-dcap-quote-verify-dev

