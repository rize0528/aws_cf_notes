---
title: Basic Cloudformation Usage
tags: aws, cloudformation, stack
---

# Useful commands
1. Validate cf templates
```
aws cloudformation validate-template --template-body <TEMPLATE_FILEPATH>
```
# Function Lists
* **!Ref**
    * Reference a value defined in cf.
    * Example:
        * ```!Ref Env```
* **!Sub**
    * Formatting a value with given template.
    * Example: 
        * ```BucketName: !Sub "aws-${Env}-${AWS::Region}-ml-model"```
* **!FindInMap**
    * Find a paired value defined in Mapping section.

# Parameters

## Define a static parameter
* Add an unique name in **Parameters** section
* Specify the value type, such as **String**,
* Visit [**Parameters**](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) for details
```yaml
Parameters:
    Description: The product env type
    Type: String
    Value: dev
```
## Define a parameter referred to SSM parameter
* By placing the value in **Type** of a parameter to
    * Example:```AWS::SSM::Parameters::Value<String>```

## Variable Mapping
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