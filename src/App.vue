<script setup>
import {onMounted, ref} from "vue";
import { S3Client, PutObjectCommand } from '@aws-sdk/client-s3'
import { fromCognitoIdentityPool } from "@aws-sdk/credential-providers";
const bucketName = 'print-md-bucket';
const region = 'ap-southeast-1'
const s3Client = new S3Client({
  region: region,
  credentials: fromCognitoIdentityPool({
    clientConfig: { region: region },
    identityPoolId: 'ap-southeast-1:3ee58368-c9eb-4d54-8991-88d249855850'
  })
});

// const command = new ListObjectsCommand({ Bucket: bucketName });
// s3Client.send(command).then(({ Contents }) => {
//   console.log(Contents);
// });
const uploading = ref('点击按钮开始上传')
onMounted(() => {

  // 获取文件选择框元素
  const fileInput = document.getElementById('file-input');
  fileInput.addEventListener('change', async (e) => {

    // const command = new CreateBucketCommand({ Bucket: bucketName });
    // await s3Client.send(command);
    const file = e.target.files[0];
    const key = file.name;

    // let uploadId;
    // const multipartUpload = await s3Client.send(
    //     new CreateMultipartUploadCommand({
    //       Bucket: bucketName,
    //       Key: key,
    //     }),
    // );
    // uploadId = multipartUpload.UploadId;
    //
    // const uploadPartCommand = new UploadPartCommand({
    //   Bucket: bucketName,
    //   Key: key,
    //   UploadId: uploadId,
    //   Body: file,
    //   PartNumber: 1,
    // })
    // s3Client.send(uploadPartCommand)
    //   .then((d) => {
    //     console.log(d);
    //   })
    uploading.value = '上传中，切勿刷新页面，请耐心等待';
    const startTime = new Date().getTime();
    const data = await s3Client.send(new PutObjectCommand({
      Bucket: bucketName,
      Body: file,
      Key: key
    }));
    uploading.value = '上传完成'
    const endTime = new Date().getTime();
    const time = ((endTime - startTime) / 1000).toFixed(2);
    alert('耗时=' + time + '秒，  文件大小 = ' + (file.size / 1024 / 1024) + 'MB',)
    console.log(`https://${bucketName}.s3.${region}.amazonaws.com/${key}`);
  })
})
</script>

<template>
  <p>{{ uploading }}</p>
  <input id="file-input" type="file">
</template>

<style scoped>

</style>
