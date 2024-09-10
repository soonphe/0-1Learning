npm install axios

```
<template>
  <div>
    <button @click="startRecording">开始录音</button>
    <button @click="stopRecording" :disabled="!isRecording">停止录音</button>
  </div>
</template>
 
<script>
import axios from 'axios';
 
export default {
  data() {
    return {
      mediaRecorder: null,
      isRecording: false,
      recordedBlobs: [],
    };
  },
  methods: {
    startRecording() {
      const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
      this.mediaRecorder = new MediaRecorder(stream, { mimeType: 'audio/webm' });
      this.mediaRecorder.ondataavailable = event => {
        if (event.data && event.data.size > 0) {
          this.recordedBlobs.push(event.data);
        }
      };
      this.mediaRecorder.start();
      this.isRecording = true;
    },
    async stopRecording() {
      this.mediaRecorder.stop();
      this.isRecording = false;
      const blob = new Blob(this.recordedBlobs, { type: 'audio/webm' });
      this.recordedBlobs = [];
      // 上传录音文件到服务器
      const formData = new FormData();
      formData.append('audio', blob, 'recording.webm');
      try {
        const response = await axios.post('/upload-audio-endpoint', formData, {
          headers: {
            'Content-Type': 'multipart/form-data',
          },
        });
        console.log('Audio uploaded:', response.data);
      } catch (error) {
        console.error('Error uploading audio:', error);
      }
    },
  },
};
</script>
```

首先通过getUserMedia获取音频流，然后创建MediaRecorder实例来录音。录音数据通过ondataavailable事件回调捕获，收集到的Blob数据块被存储在recordedBlobs数组中。当用户点击停止按钮时，我们调用stop方法停止录音，并通过FormData将录音文件打包成一个可以上传的格式，最后使用axios发送POST请求上传到服务器。

请注意，上述代码中的/upload-audio-endpoint是假定的服务器上传API端点，你需要根据实际情况替换为你的服务器地址。同时，服务器端需要能够处理multipart/form-data类型的POST请求，并保存上传的文件。

