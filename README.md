# readme
# Evaccuracy runcnns

# Introduction
The evaccuracy is used to run evaccuracy runcnn command in batch manner. 

# How to setup
## Directory structure of dataset
### Classify:
ImageNet
~~~
/parent_folder/
├── val
│   ├── *.JPEG images
├── test
│   ├── *.JPEG images
└── val.txt/test.txt  -> download by synopsys-caffe/data/ilsvrc12/get_ilsvrc_aux.sh
~~~
### Detection:
* Coco
* Voc
### Segment:
Cityscape
# How to run 
## 1 the command 
~~~
usage: evaccuracy runcnns [-h] --config CONFIG [--output OUTPUT]

MWEV cnn graph examples runner with graph output layer binary dumping function

optional arguments:
-h, --help       show this help message and exit
--config CONFIG  Example runner configuration file in json format
--output OUTPUT  Path of folder to save cnn runner results
~~~


## 2 the format of input json file 
1. The json file contain the configuration information that guide the evaccuracy runcnn，the below is a simple example of json file
~~~
{
    "global_config" : {
        "run_all_model_names": false, 
        "maps_format": ["12bit", "8bit"], 
        "evgencnn_options": {"tofino": "", "toronto": "--acc_shift 0 --Zweight_scale true --Zblob_scale true"},
        "ev_config":
            ["ev_native", "host_fixed", 0, false]
        ], 
        "config": [], 
        "jobs": 10, 
        "cnn_dataset_root": "/slowfs/us01dwt2p832/datasets",
        "target": "clean install"
    },
    "examples": {
        "examples/classification/GoogleNet" : { "subset_10": {
            "imgfolder": "ImageNet/ILSVRC2012",
            "imglist": "ImageNet/ILSVRC2012/2012_val.txt",
            "maxcnt": 10,
            "evaccuracy" : {
                "type": "classify",
                "config_file": "googlenet.cfg",
                "extra_options": ""
            }
        }, "subset_10_cali": {
            "imgfolder": "ImageNet/ILSVRC2012",
            "imglist": "ImageNet/ILSVRC2012/2012_val.txt",
            "cali_imgfolder": "ImageNet/ILSVRC2012",
            "cali_imglist": "ImageNet/ILSVRC2012/2012_val_cali_1000.txt",
            "maxcnt": 10,
            "evaccuracy" : {
                "type": "classify",
                "config_file": "googlenet.cfg",
                "extra_options": ""
            }
        } },
"examples/detection/MobileNet-SSD" : { "voc2007_test_fullset": {
            "imgfolder": "VOCdevkit/VOC2007",
            "imglist": "VOCdevkit/voc2007_test_imglist.txt",
			"maxcnt": 10,
            "evaccuracy" : {
                "type": "detect",
                "config_file": "mobilenet_ssd_300x300.cfg",
                "extra_options": "--eval_type voc2007 --dataset_type test --min_overlap 0.5"
            }
        }, "voc2007_test_fullset_cali_500": {
            "imgfolder": "VOCdevkit/VOC2007",
            "imglist": "VOCdevkit/voc2007_test_imglist.txt",
            "cali_imgfolder": "VOCdevkit/VOC2007",
            "cali_imglist": "VOCdevkit/voc2007_cali_500.txt",
			"maxcnt": 10,
            "evaccuracy" : {
                "type": "detect",
                "config_file": "mobilenet_ssd_300x300.cfg",
                "extra_options": "--eval_type voc2007 --dataset_type test --min_overlap 0.5"
            }
        } }
    }
}
~~~
2. The json file could divide into two part,
The first part is the global configuration, the second part is local configuration for local examples, when a parameter have been defined in both global and local part, the local part configuration have a high level component
The parameters of global part 
```
  'run_all_model_names' : default False
  'maps_format' : 8bit or 12bit
  'make_options' :global_config_set.get('make_options', {})
  'ev_config' :  four option, they are EVSS_CFG, CNN_ABSTRACTION,EVSS_DBG,
  'config' : []
  'jobs' : the work jobs 
  'fixed_dumpdir_root' :
  'cnn_dataset_root' : the root combine with the path in local is the complete dataset path
  'pre_clean' : default is False
  'evgencnn_options' :
  'target' : install or 'clean install'
  
```
3. The local part 
The local part configuration is separate for each graph in cnn_tools/example, and you could add some other parameters combine with gloable parameters to run evaccuracy runcnn.
```
    dumpdir: default is current path 
    model_name: different model in example/configs
    imgfolder": "VOCdevkit/VOC2007
    imglist": "VOCdevkit/voc2007_test_imglist.txt
    cali_imgfolder: calibration image folder
    cali_imglist: calibration image list
    maxcnt: sample maxcnt number images for test 
    'evaccuracy'
    
```
for 'evaccuracy' there are three child_options, 
* type: classify/detect/segment
* config_file": you could found the config file in the evgencnn/scripts/evaccuracy/imagenet etc. 
* extra_options": for evaccuracy classify/detect/segment, because of they are differents task and the commands is little differen ,we use the exa_options to cover the difference. when run evaccuracy classify/segment the  exa_options is null and for evaccuracy detect, you should add extral parameters


# Command Output
Evaccuracy runcnns will create the following file 


1. evaccuracy_analyzed.json
```
this file contain the parameters of each example 
```
2. evaccuracy_analyzed.xls
```
this file contain the results of evaccuracy classify/detect/segment
```
3. evaccuracy_runcnns.json
4. evaccuracy_runcnns.log
```
this file contain the results show in the terminal,
```
5. evaccuracy_runcnns_config.json
