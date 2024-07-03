<script setup>
import { onMounted } from "vue";
import { S3Client, PutObjectCommand, ListObjectsCommand } from '@aws-sdk/client-s3'
import { fromCognitoIdentityPool } from "@aws-sdk/credential-providers";
const bucketName = 'print-md-bucket';

const s3Client = new S3Client({
  region: 'ap-southeast-1',
  credentials: fromCognitoIdentityPool({
    clientConfig: { region: "ap-southeast-1" },
    identityPoolId: 'ap-southeast-1:3ee58368-c9eb-4d54-8991-88d249855850'
  })
});

const command = new ListObjectsCommand({ Bucket: bucketName });
s3Client.send(command).then(({ Contents }) => {
  console.log(Contents);
});

onMounted(() => {

  // 获取文件选择框元素
  const fileInput = document.getElementById('file-input');
  fileInput.addEventListener('change', async (e) => {

    // const command = new CreateBucketCommand({ Bucket: bucketName });
    // await s3Client.send(command);
    // const file = e.target.files[0];
    const data = await s3Client.send(new PutObjectCommand({
      Bucket: bucketName,
      Body: 'Hello S3!',
      Key: 'hello-s3.txt'
    }));
    console.log(data);
  })
})
</script>

<template>
  <input id="file-input" type="file">
</template>

<style scoped>

</style>
