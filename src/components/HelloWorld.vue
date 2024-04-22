<template>
  <n-layout has-sider>
    <n-layout-sider bordered collapse-mode="width" :collapsed-width="0" trigger-style="top: 5px;"
      collapsed-trigger-style="top:5px;right: -20px;" :width="50" :collapsed="collapsed" show-trigger="arrow-circle"
      @collapse="collapsed = true" @expand="collapsed = false">
      <n-menu v-model:value="activeKey" :collapsed="collapsed" :indent="15" :collapsed-width="0"
        :collapsed-icon-size="12" :icon-size="20" :options="menuOptions" />
    </n-layout-sider>
    <n-layout>
      <n-upload action="https://www.mocky.io/v2/5e4bafc63100007100d8b70f" @before-upload="beforeUpload">
        <n-button>上传 PNG 文件</n-button>
      </n-upload>
      <Editor />
    </n-layout>
  </n-layout>
</template>

<script>
/* import Editor from "./Editor.vue"; */
import { defineComponent, h, ref } from "vue";
import { NIcon, useMessage } from "naive-ui";
import {
  BookOutline,
  ChatboxEllipsesOutline
} from "@vicons/ionicons5";

function renderIcon(icon) {
  return () => h(NIcon, null, { default: () => h(icon) });
}

const menuOptions = [
  {
    label: "",
    key: "chat",
    icon: renderIcon(ChatboxEllipsesOutline)
  },
  {
    label: "",
    key: "data-transfer",
    icon: renderIcon(BookOutline)
  },
];

export default defineComponent({
  setup() {
    const message = useMessage();
    return {
      activeKey: ref(null),
      collapsed: ref(true),
      menuOptions,
      async beforeUpload(data) {
        if (data.file.file?.type !== "image/png") {
          message.error("只能上传png格式的图片文件，请重新上传");
          return false;
        }
        return true;
      }
    };
  },
  components: [
    /* Editor */
  ]
});
</script>