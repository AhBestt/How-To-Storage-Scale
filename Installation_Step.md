# Step to Installation Storage Scale

1. Transfer ISO file to Bastion, Spectrum Node1, Spectrum Node2
   
2. Mount and Create local repo on Bastion, Spectrum Node1, Spectrum Node2

3. Setup Pre-requisite on Bastion, Spectrum Node1, Spectrum Node2
  -  Login as root
  -  Configure /etc/hosts add following
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
    -  Add following in /etc/multipath.conf
      ```bash
       devices {
         device {
           vendor "IBM"
       
