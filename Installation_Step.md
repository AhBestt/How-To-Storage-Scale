# Step to Installation Storage Scale

1. Transfer ISO file to Bastion, Spectrum Node1, Spectrum Node2
___   
2. Mount and Create local repo on Bastion, Spectrum Node1, Spectrum Node2
___
3. Setup Pre-requisite on Bastion, Spectrum Node1, Spectrum Node2
  -  Login as root
  -  Configure `/etc/hosts` add following
      ```bash
        <ip_bastion> <Bastion_name>
        <ip_spectrum_node1> <Spectrum_Node1_name>
        <ip_spectrum_node2> <Spectrum_Node2_name>
        <ip_backend_spectrum_node1> <Spectrum_Node1_Backend_name>    #  In case you use backend
        <ip_backend_spectrum_node2> <Spectrum_Node2_Backend_name>    #  In case you use backend
  -  Create SSH-key pair and Copy to Other Node
      -  `ssh-keygen`
      -  `ssh-copy-id <Bastion_name>`
      -  `ssh-copy-id <Spectrum_Node1_name>`
      -  `ssh-copy-id <Spectrum_Node2_name>`
  - Check Storage
    -  `multipath -ll`
    -  In case you didn't see storage run `scsi-rescan`
    -  Add following in `/etc/multipath.conf`
      ```bash
       devices {
         device {
            vendor "IBM"
            product "2145"
            path_grouping_policy group_by_prio
            prio alua
            path_checker tur
            hardware_handler "1 alua"
            failback immediate
            no_path_retry 60
            rr_weight uniform
            rr_min_io 1000
         }
      }
      ```
      -   check multipath <br> `multipath -ll | grep 2145`
      -   Mapping storage ID
   -   Configure NTP from `/etc/chrony.conf`
        -   replace as following (looking for pool -> you can remove or just #)
          ```bash
          # pool .....   iburst
          server <ip_ntp_server> iburst
          ```
          `systemctl restart chronyd` <br>
          `timedatectl`
        -   Stop firewall, selinux
          `systemctl stop firewalld`<br>
          `systemctl disable firewalld`<br>
          `setenforce 0`<br>
          `getenforce`
          -   configure file `/etc/selinux/config` change from enable to disable
                ```bash
                SELINUX=disabled
                ```
   -   Check DNS on Spectrum Node1 <br>
        `dig <FQDN_Spectrum_node1_name>` <br>
        `dig <FQDN_Spectrum_node2_name>`
___
4.   Install package on Spectrum Node1, Spectrum Node2<br>
      `yum install kernel-devel cpp gcc gcc-c++ binutils elfutils elfutils-devel -y`
___
5.   Install on Bastion <br>
      -  Go to path Storage_Scale file that you prepared <br>
      `./Storage_Scale_Data_Management-5.2.3.0-x86_64-Linux-install` <br>
      -  Go to path ansible
      `cd /usr/lpp/mmfs/5.2.3.0/ansible-toolkit`<br>
      `./spectrumscale setup -s <ip_bastion>` <br>
      `./spectrumscale node add <Spectrum_Node1_name> -a -g -n -m` # -a = admin , -g = gui, -n = node, -m = manager <br>
      `./spectrumscale node add <Spectrum_Node2_name> -a -g -n` <br>
      `./spectrumscale callhome disable` # in case you don't use callhome <br>
      -  If you want to config you can go to `ansible/vars/scale_clusterdefinition.json` for example change scale_daemon_node_name <br>
      -  run precheck if it's show any error. Debug and fixed it <br>
      `./spectrumscale install --precheck` <br>
      -  After you run precheck and it's not have any error, you can run install <br>
      `./spectrumscale install`<br>
      -  Add all nsd you want (default name start from nsd1) <br>
      `./spectrumscale nsd add -p <Spectrum_Node1_name> -s <Spectrum_Node2_name> -u dataAndMetadata "</dev/dm-x>"` <br>
      -  If you want to change name <br>
      `./spectrumscale nsd modify <nsd_old_name> --name "<nsd_new_name>"` <br>
      
