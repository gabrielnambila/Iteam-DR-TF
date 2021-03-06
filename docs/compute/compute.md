

# Resource Stack Edits for Compute and Volume


## Necessary Information

### Variables to pull from the OCI Console

- Identification: This information allows the user to apply the terraform commands to create these resources from the terminal. If you would rather upload the terraform files directly to the OCI console, you do not need these values and do not need to update them in provider.tf or vars.tf
    - API fingerprint
    - api_private_key_path
    - Tenancy OCID
    - User OCID
    - Compartment OCID
- Primary Region
    - Region Name (ex: eu-amsterdam-1)
    - VCN OCID              
    - VCN IP                
    - Subnet IP                  

### New Variables

- Select Standby Region     
    - Standby Region Name 
- Standby Region Networking
    - Standby VCN CIDR (Note: has to be non-overlapping to Primary Region VCN to allow for Peering)
    - Standby Subnet CIDR 
 -    Instance Shape
      - needs to be defined for each compute shape                                              


## OCI Console Steps

 Download Resource Stack. Include availability_domain, core, and Instance modules.
 
## Changes to make to .tf files

### Generic Changes

1. Remove Defined Tags exported with each resource. They have been automatically marked with the time the resource was created which will not be accurate for the standby environment.

2. Change Resource Names and Display Names. This is optional. If you choose to change the resource names, be sure to change them throughout the files since the resources often refer to each other. 
WORKING NOTE: DO YOU MAYBE HAVE TO CHANGE THESE VALUES? IS KEEPING ALL THE VALUES THE SAME PERHAPS WHAT IS CAUSING MY DNSRECORD ERROR?



### INSTANCE.TF

Create Compute Instance

 Fill in variables for:
 - availability domain
 - compartment
 - instance name
 - instance shape
 - Image Used
 - specify OCPU and RAM count

```
resource "oci_core_instance" <Instance Name> {
    #Required
    
    availability_config {
    recovery_action = "RESTORE_INSTANCE"
    }
    
    availability_domain = <Standby Availability Domain>
    compartment_id = <Standby Compartment Name>
    shape = <Instance Shape>

    create_vnic_details {
        #Optional Configuration
        assign_public_ip = <True or False>
        display_name = <Instance Name>
        hostname_label = <Instance Name>
        private_ip = <Private IP address>
        subnet_id  = <Subnet ID>
    }

    shape_config {
    #customize here
    memory_in_gbs = "1"
    ocpus         = "1"
   }

    source_details {
        #Required
        source_id = <Source Image for Instance>
        source_type = "image"
    }
   ```
