<script lang="ts" setup>
import { defineComponent, onMounted, ref } from "vue"

interface FileWithURL {
  file: File;
  url: string;
}

const fileInput = ref<HTMLInputElement>()
const fileMap = ref(new Map<string, FileWithURL>())

/**
 * 当 input[type='file'] 变化，即用户选择了文件的使用，将其以文件名为 key ，暂时保存在 fileMap 中；
 * 注意， input 上的 files 属性，只保存用户打开了文件选择框，并且选择了文件后，这一时刻的文件
 * 也就是说，用户每次选择执行的都是 replace 的操作，因此，我们使用 map 来维护用户多次选择的所有文件，以及一些手动删除的操作
 */
const onFileChange = function(this: HTMLInputElement) {
  const files = this.files || [];
  for (const file of files) {
    const url = URL.createObjectURL(file)
    fileMap.value.set(file.name, { file, url })
  }
}

/** 点击模拟的文件选择触发按钮，触发真实的文件选择的响应事件 */
const clickFileIput = () => {
  fileInput.value?.click()
}

onMounted(() => {
  fileInput.value?.addEventListener("change", onFileChange)
})
</script>

<template>
  <div>
    <input
      ref="fileInput"
      type="file"
      placeholder="选择你的文件"
      style="display:none;"
      multiple
    >
    <div class="file-input">
      <div
        v-for="item in fileMap.values()"
        :key="item.file.name"
        class="img-preview"
      >
        <img :src="item.url">
      </div>
      <i class="select-file iconfont" @click="clickFileIput">&#xe668;</i>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.file-input{
  display: flex;
  flex-direction: row;
  padding: 12px;

  .img-preview {
    position: relative;
    margin: 0 6px;

    img {
      width: 100px;
      height: 100px;
    }
  }

  .select-file {
    width: 100px;
    height: 100px;
    color: #eee;
    font-size: 70px;
    line-height: 100px;
    text-align: center;
    background-color: white;
    border: 1px solid #ddd;
    border-radius: 8px;
  }
}
</style>
