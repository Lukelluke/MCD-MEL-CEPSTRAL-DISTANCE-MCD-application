# MCD-toolkit-polished-useful
Use Merlin toolkit to convert .wav files to .gcm files, and modified original MCD implement.


1. install Merlin toolkit as https://github.com/CSTR-Edinburgh/merlin taught.(to extract some useful features like .bap/.lf0/.mgc)
2. cd into merlin/tool , and install as new script under that file
3. cd into merlin/tools/SPTK*3.9, follow INSTALL md taught( because we mainly use sptk toolkit to extract features)
4. cd merlin/egs/voice_conversion/si
5. ./01_setup.sh speakerA speakerB (it will mkdir to files named database/speakerA && apeakerB)

***

6. ./02_prepare_acoustic_features.sh <path_to_wav_dir> <path_to_feat_dir> (you need to mkdir two new folder to contain the features to be extracted, recommend to build under database folder)
7. ![image-20200718155047663](https://blog-1301959139.cos.ap-beijing.myqcloud.com/picGo/20200718155049.png)

8. then u got two .mgc files

***

9. install MCD tools as https://github.com/MattShannon/mcd/tree/c86266a2caf6a7cb248ea89ea56f90fd161a297e taguht



10 modify some codes in htk_io/vecseq.py, as picture show:

![image-20200718155142402](https://blog-1301959139.cos.ap-beijing.myqcloud.com/picGo/20200718155143.png)

```
    def readFile(self, vecSeqFile):
        """Reads a raw vector sequence file.

        The dtype of the returned numpy array is always the numpy default
        np.float, which may be 32-bit or 64-bit depending on architecture, etc.
        """
        Vec = np.fromfile(vecSeqFile, dtype=self.dtypeFile)
        lengthOfVec = len(Vec)
        misLenToPad = lengthOfVec % self.vecSize
        means = np.mean(Vec)

        for i in range(misLenToPad):
            Vec = np.insert(Vec, lengthOfVec, means)

        return np.reshape(
            Vec,
            (-1, self.vecSize)
        ).astype(np.float)

        # return np.reshape(
        #     np.fromfile(vecSeqFile, dtype=self.dtypeFile),
        #     (-1, self.vecSize)
        # ).astype(np.float)
```


up modified code is to solve reshape problem during read .mgc files data: original auther's algorithm here make me always counter with error:
![image-20200718155712646](https://blog-1301959139.cos.ap-beijing.myqcloud.com/picGo/20200718155714.png)

11. Here my solution is pad the mean number if .mgc into source .mgc so as to make it could be % by 40 dimension.

12. this change work well on all kind of wav files (Notice that : Merlin only accept 16bit format of wav, you can change this parameter 
by Audition or 'sox' toolkit)

13. Last but not least: we would better use 16k sample rate as our wav sr. In exp, 44100hz's MCD would be 16+ when it just 13+ in 16k hz


***

14 Ps. one more thing puzzled me: i use stargan-vc2's original demo .wav files for testing, which authority told that MCD is only 6.+ in their paper,
however i calculate then as 13+.

In another hand, i use my personal VoiceConversion tast final result for testing, can get 7.1+score, which makes sense (MCD should be among
[4, 8], the less the better )

15. Any question, or any improment suggestion, welcome to make issues!

***
 To do , release some examples
