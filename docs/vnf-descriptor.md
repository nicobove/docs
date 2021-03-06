# Virtual Network Function Descriptor

The VirtualNetworkFunctionDescriptor is contained inside the Network Service Descriptor (NSD). It is a json file and is defined as follows:

```json
{  
    "vendor":"fokus",
    "version":"0.2",
    "name":"iperf-server",
    "type":"server",
    "endpoint":"generic",
    "configurations":{
        "name":"config_name",
        "configurationParameters":[
        {
            "confKey":"key",
            "value":"value"
        }
        ]
    },
    "vdu":[  
        {  
            "vm_image":[  
                "ubuntu-14.04-server-cloudimg-amd64-disk1"
            ],
            "vimInstanceName":"vim-instance",
            "scale_in_out":2,
            "vnfc":[  
                {  
                    "connection_point":[  
                        {  
                            "floatingIp":"random",
                            "virtual_link_reference":"private"
                        }
                    ]
                }
            ]
        }
    ],
    "virtual_link":[  
        {  
            "name":"private"
        }
    ],
    "lifecycle_event":[  
        {  
            "event":"INSTANTIATE",
            "lifecycle_events":[  
                "install.sh",
                "install-srv.sh"
            ]
        }
    ],
    "deployment_flavour":[  
        {  
            "flavour_key":"m1.small"
        }
    ],
    "provides":[
        "ip",
        "source",
        "param1",
        "param2"
      ]
}
```

| Params          				| Meaning       																|
| -------------   				| -------------:																|
| name  						| The name to give to the VirtualNetworkFunctionDescriptor 						|
| vendor 						| The vendor creating this VirtualNetworkFunctionDescriptor      				|
| version 						| The version of the VirtualNetworkFunctionDescriptor (can be any string)      	|
| type	 						| The type of the VirtualNetworkFunctionDescriptor (can be any string) and it is used in the dependency parameters in the scripts      	|
| endpoint 						| The endpoint of the VirtualNetworkFunctionDescriptor (can be any string) needs to point to a registered VnfManager     	|


The other most important parameters are described in the following sections.

### Virtual Deployment Unit (VDU)

| Params          				| Meaning       																|
| -------------   				| -------------:																|
| vm_image  					| The list of image names or ids existing in the VimInstance or in the VNF Package						|
| vimInstanceName				| The VimInstance name chosen      				|
| scale_in_out					| The maximum number of instances (VMs) which can be created to support scale out/in.      	|
| vnfc                                          | This field contains a list of VNFComponents which will be deployed for this VNFD.  |

##### VNFC

After launching a network service, every VNFComponent will run on a separate virtual machine. If scaling is enabled by the scale_in_out field in the VDU, the number of instantiated VNFCs can increase.
VNFCs contain the following fields:

| Params                        | Meaning                                                                       |
| -------------                 | -------------:                                                                |
|connection_point               | each connection point is a reference to an Internal Virtual Link (see Connection Point at [ETSI NFV][nfv-mano]). Moreover you can specify a floatingIp to be assigned to this connection point. The possible values are the actual floatingip ip or "random" if no preference is specified. If omitted no floatingip will be assigned.    	        |

### Configurations

The configuration object contains a list of parameters defined by key, value that can be used in the scripts (see [VNF Parameters][vnf-parameters]).

### Virtual Link (aka Internal Virtual Link)

The Internal Virtual Link points to a Virtual Link Descriptor defined in the Network Service Descriptor.

**Note:** at the moment there are no difference between internal and external VL. All the available networks should be specified in the NSD->VLD, then in the VNFD->VL you specify which networks you want to use.
Finally in the VNFD->VDU->VNFC->connection_point you can specify which network to attach among those available in VNFD->VL. The virtual_link_reference parameter also must be filled with the exact same links.

### Lifecycle Events

A lifecycle event is composed by an Event and a list of strings that correspond to the script names needed to be run in that particular Event.
Currently supported events are:

| Event name    | Description |
| ---------     | ---------:  |
| INSTANTIATE   | ...         |
| CONFIGURE     | ...         |
| START         | ...         |
| TERMINATE     | ...         |
| SCALE_IN      | ...         |

The VNF events state machine follows the  state diagram for the VNFR (and NSR) displayed in [this slide][vnf-state-slide] 

### Deployment Flavour

A delpoyment flavour corresponds to a flavour name existing in the VimInstance.
For example if you are using Openstack as Vim, the flavour_key parameter shall correspond to a [flavour name of Openstack][openstack-flavours] (e.q. m1.small).

### Provides, Requires

This list of parameter names defines the parameters that the VnfManager will fill at runtime. For that reason they have a meaning only if you [write your own VnfManager][vnfm-how-to]. This parameters are then available in any scripts. For the usage of the parameters, please, refer to [How to use the parameters][param-how-to] page.

<!---
References
-->

[nfv-mano]: http://www.etsi.org/deliver/etsi_gs/NFV-MAN/001_099/001/01.01.01_60/gs_NFV-MAN001v010101p.pdf
[param-how-to]: vnf-parameters
[vnf-parameters]:vnf-parameters
[vnfm-how-to]: vnfm-how-to-write
[vnf-package-link]: vnfpackage
[openstack-flavours]: http://docs.openstack.org/openstack-ops/content/flavors.html
[vnf-state-slide]: http://image.slidesharecdn.com/nfvvnfarchitecturepresentation-141006041349-conversion-gate01/95/nfv-virtual-network-function-architecture-22-638.jpg?cb=1436628676


<!---
Script for open external links in a new tab
-->
<script type="text/javascript" charset="utf-8">
      // Creating custom :external selector
      $.expr[':'].external = function(obj){
          return !obj.href.match(/^mailto\:/)
                  && (obj.hostname != location.hostname);
      };
      $(function(){
        $('a:external').addClass('external');
        $(".external").attr('target','_blank');
      })
</script>
