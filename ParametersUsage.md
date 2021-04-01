---
title: Basic Cloudformation Usage
tags: aws, cloudformation, stack
---

# Basic cloudformation usage

## Parameters

### Variable Mapping
* Define the mapping pairs within [**Mappings**](https://docs.aws.amazon.com/zh_tw/AWSCloudFormation/latest/UserGuide/mappings-section-structure.html) section.
* Access the mapped value with [**!FindInMap**](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-findinmap.html) function.

Below yaml snippet demonstrate how to use the mapping value with **!FindInMap**
```yaml
Parameters:
    EnvironmentType:
        Description: The product env type
        Type: String
        Default: dev
        AllowedValues:
            - dev
            - int
            - stg
            - prod
        ConstraintDescription: "must be any stage of product environment."
Mappings:
    RegionalURLMap:
        us-east-1:
            dev: "us/dev/index.html"
            prod: "us/prod/index.html"
            machineType: "t4g.micro"
        ap-northeast-1:
            dev: "jp/dev/index.html"
            prod: "jp/prod/index.html"
            machineType: "t2.micro"
Resources:
    EC2Instance:
        Type: "AWS::EC2::Instance"
        Properties:
            InstanceType: !FindInMap [RegionalURLMap, !Ref "AWS::Region", machineType] 
Outputs:
    MyOutput: 
        Description: Return the URL of selected region and environment
        Value: !FindInMap [RegionalURLMap, !Ref "AWS::Region", !Ref EnvironmentType]
```
![](https://i.imgur.com/oiIF1Qv.jpg)