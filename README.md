# Evaccuracy runcnns

# Introduction
The evaccuracy is used to run evaccuracy runcnn command and do the evaccuracy in batch manner. you could do evaccuracy runcnn on a example graph and then do the evaccuracy classify/detect/segment by the predefined config file. for the evacccracy runcnn/classify/detect/segment you could reference to  [readme](https://gitsnps.internal.synopsys.com/dwc_ev/cnn_tools/blob/dev_accuracy/evgencnn/scripts/evaccuracy/README.md).   
to run evaccuracy runcnns you should do those prepare works
1. prepare the dataset 
2. prepare the configretion file
## Directory structure of dataset
the format of those dataset for classify/detect/segment please refer to [imagenet](https://gitsnps.internal.synopsys.com/dwc_ev/cnn_tools/blob/dev_accuracy/evgencnn/scripts/evaccuracy/imagenet/README.md),[(coco,voc)](https://gitsnps.internal.synopsys.com/dwc_ev/cnn_tools/blob/dev_accuracy/evgencnn/scripts/evaccuracy/map_calculation/README.md),[(voc,cityscape)](https://gitsnps.internal.synopsys.com/dwc_ev/cnn_tools/blob/dev_accuracy/evgencnn/scripts/evaccuracy/segmentation/README.md).    we have add another part called calibration which is used to verify the results of evaccuracy.
### Classify:
ImageNet
~~~
ImageNet/ILSVRC2012(parent_folder)/
├── val
│   ├── *.JPEG images
└── 2012_val.txt/2012_val_cali_2000.txt
~~~
### Detection:
* Coco
~~~
coco(parent_folder)/
├── val2017
├── test2015
├── val2014
│   ├── *.JPEG images

|─ cocoval2017.txt
~~~
* Voc
~~~
VOCdevkit(parent_folder)/
├── VOC2007
│   ├── ImageSets
│       ├── *.JPEG images
│   ├── Annotations
│   ├── SegmentationObject
│   ├── JPEGImages
│   ├── Cali
|─ voc2007_test_imglist.txt/ voc2007_cali_500.txt
~~~
### Segment:
* Cityscape
~~~
cityscapes(parent_folder)/
├── leftImg8bit
│   ├── *.JPEG images
├── gtCoarse
├── ICNet
|─ icnet_fineval.txt
~~~
* voc
~~~
VOCdevkit(arent_folder)/
├── VOC2007
│   ├── ImageSets
│   	├── *.JPEG images
│   ├── Annotations
│   ├── SegmentationObject
│   ├── JPEGImages
│   ├── Cali
|─ seg11valid_test.txt/ seg11valid_test_cali_100.txt
~~~
* CamVid
~~~
CamVid(parent_folder)/
├── train
├── trainannot
├── val
├── valannot
│   ├── ImageSets
|─ test.txt/ val.txt
~~~
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
* the `config` means the configration file that predefined in the input json file. the json file contain the parameters about the evaccuracy runcnn and evaccuracy classify/segment/detect   
* the `output` is the output folder of evaccuracy runcnns, it contains the results of ecaccuracy runcnn and evaccuracy classify/detect/segment.
## 2 the format of input json file 
The json file contain the configuration information that guide the evaccuracy runcnn and evaccuracy classify/detect/segment，and we will give the format of json file detailly. The json file could divide into two parts, The first part is the global configuration, the second part is local configuration for local examples, when a parameter have been defined in both global and local part, the local part configuration have a high level component. for the means of these pararemeters, please refere to the doc files[]. 
1. The parameters of global part      
the gloabal part have the following parameters:
* **run_all_model_names**: the option is used to contral whether run all the model exist in example/configs
  - **type**: string type, choose False or True and the default is False
* **maps_format**: this parameters decide how much bit-dumps the evaccuracy runcnns will done, and we only support 8 and 12 bit now.
  - **type**:: list type, it should be like ["8bit"], ["12bit"] or ["8bit", "12bit"] 
* **make_options**: 
  - **type**: 
* **ev_config**: there are four option, they are EVSS_CFG, CNN_ABSTRACTION,EVSS_DBG, run_haps
  - **type**: list type, you should give a list like ["ev_native", "host_fixed", 0, false]
* **jobs**: how many jobs
  - **type**: int type
* **fixed_dumpdir_root**: the root path of fixed_dump
  - **type**: string type
* **evgencnn_options**: we provide two kinds options, tofinno and toronto. for toronto you should add 
  - **type**: string type, {"tofino": "", "toronto": "--acc_shift 0 --Zweight_scale true --Zblob_scale true"},
* **target**: build option, clean-install or install
  - **type**: string type, 
 the below is a global config example
```
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
  
```
2. The local part       
The local part configuration is separate for each graph in cnn_tools/example, and you could add some other parameters combine with gloable parameters to run evaccuracy runcnn.
```
    'dumpdir': default is current path 
    'model_name': different model in example/configs
    'imgfolder'": "VOCdevkit/VOC2007 
    'imglist": "VOCdevkit/voc2007_test_imglist.txt
    'cali_imgfolder': calibration image folder
    'cali_imglist': calibration image list
    'maxcn't: sample maxcnt number images for test 
    'evaccuracy': extral option to cover differen task
    
```
for 'evaccuracy' there are three child_options, 
* type: classify/detect/segment
* config_file: you could found the config file in the evgencnn/scripts/evaccuracy/imagenet etc. 
* extra_options: for evaccuracy classify/detect/segment, because of they are differents task and the commands is little differen ,we use the exa_optio ns to cover the difference. when run evaccuracy classify/segment the  exa_options is null and for evaccuracy detect, you should add ext ral parameters
the below is a simple example of json file
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
