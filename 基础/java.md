# 第一个markdown文档
* 第一点
* 第二点
* 第三点
* 第四点

```java
public FileMeta saveFile(String uid, String fhash, String fname, String ftype, byte[] fdata) throws BizException {
    ParamsChecker.checkNotBlank(uid, "no uid");
    ParamsChecker.checkNotBlank(fhash, "no fhash");
    ParamsChecker.checkNotBlank(fname, "no fname");
    ParamsChecker.checkNotBlank(ftype, "no ftype");
    ParamsChecker.checkNotEmpty(fdata, "no fdata");

    // check fname
    String extName = Utils.getFileExtName(fname);
    BizResult.ensureTrue(extName.equalsIgnoreCase(ftype), Errors.ERR_SYS_PARAMS_WRONG, "fname wrong, conflict of ftype");

    // check fhash
    String fdataSha256;
    try {
        fdataSha256 = EncodeUtils.sha256(fdata);
    }
    catch (NoSuchAlgorithmException e) {
        throw new BizException(Errors.ERR_SYS_EXCUTE_WRONG, "execute wrong: " + e.getMessage(), e);
    }
    BizResult.ensureTrue(fdataSha256.equals(fhash), Errors.ERR_SYS_PARAMS_WRONG, "fhash of fdata wrong");

    if (ftype.equalsIgnoreCase("pdf")) {
        BizResult.ensureTrue(PdfUtils.isPdf(fdata), Errors.ERR_SYS_PARAMS_WRONG, "not a " + ftype);
    }

    String fileCode;
    try {
        fileCode = fileLogic.saveFile(fname, ftype, fdata, uid);
    }
    catch (NoSuchAlgorithmException | IOException e) {
        throw new BizException(Errors.ERR_SYS_EXCUTE_WRONG, "execute wrong: " + e.getMessage(), e);
    }
    fileLogic.setFileOwner(fileCode, uid);
    return fileLogic.getFileMeta(fileCode);
}
```
