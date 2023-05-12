# Android_Decoder_Selection_Library

Choose the right Android decoder for a given video file. 

## Name
Dolby Vision Decoder Selection Tool

## Description
This library takes a video file as input. The code will examine the matadata in the file to establish which formats are present on the video track(s) that are present in the video file. The next step the tool takes is to ask the android device for all codecs present. The selection is done by looking at the tracks in numerical order and selecting the first one that the current device is capable of playing. The tool then assembles the appropriate video decoder and this is made available to the user. 

## Installation
The code available in this repository can be opened as an Android Studio project. Once this is done, build the app as normal. The output by default will not be an APK but rather an AAR file or Android Archive. This can then be linked to another project allowing the tool to be used as a library. 

## Usage
'''
CodecBuilderImpl builder = new CodecBuilderImpl(this.getAppContext(), this.getInputUri(), false);

            String codecName = builder.getCodecName();

            this.createCodec(MediaCodec.createByCodecName(codecName));

            Log.e("videoDecoder", "init: CodecSel " + codecName);

            this.extractor = builder.configure(this.getCodec(), outputSurface,this, transfer);
'''



## Authors and acknowledgment
Author: Dolby Laboratories

## License
BSD Clear License

