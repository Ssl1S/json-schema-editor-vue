<template>
  <div id="app">
    <div class="title">
      <a href="https://github.com/zyqwst/json-schema-editor-vue" target="_blank"
        >json-schema-editor-vue</a
      >
      <span class="version"> version：{{ version }}</span>
    </div>
    <div class="desc">
      <div>
        A json-schema editor of high efficient and easy-to-use, base on Vue.
        <a @click="visible = true">import json</a>
        <span style="margin-left: 20px;">
          <label>
            <input type="checkbox" v-model="liveUpdate" />
            实时渲染 JSON
          </label>
        </span>
      </div>
    </div>
    <div class="container">
      <codemirror v-if="liveUpdate" class="code" v-model="jsonStr" :readOnly="false" />
      <json-schema-editor
        :class="liveUpdate ? 'schema' : 'schema-full'"
        :value="tree"
        disabledType
        lang="zh_CN"
        custom
        :extra="extraSetting"
      />
    </div>
    <a-modal
      v-model="visible"
      title="import json"
      width="800px"
      height="600x"
      @ok="handleImportJson"
    >
      <div class="code-container">
        <codemirror class="code" v-model="importJson" :readOnly="false" />
      </div>
    </a-modal>
  </div>
</template>

<script>
var app = require("../package.json");
import Codemirror from "./components/Codemirror.vue";
import GenerateSchema from "generate-schema";
export default {
  name: "App",
  components: { Codemirror },
  computed: {
    jsonStr: {
      get: function () {
        return JSON.stringify(this.tree, null, 2);
      },
      set: function (newVal) {
        this.tree = JSON.parse(newVal);
      },
    },
  },
  data() {
    return {
      version: app.version,
      importJson: "",
      visible: false,
      liveUpdate: true,
      extraSetting: {
        integer: {
          default: {
            name: "默认值",
            type: "integer",
          },
        },
        string: {
          default: {
            name: "默认值",
            type: "integer",
          },
        },
      },
      tree: {
        name: "user_data",
        strict: true,
        schema: {
          type: "object",
          description: "条件",
          properties: {
            name: {
              type: "string",
              description: "名称",
              maxLength: 10,
              minLength: 2,
            },
            appId: {
              type: "integer",
              description: "应用ID",
              default: 3,
            },
            credate: {
              type: "string",
              description: "创建日期",
              format: "date",
            },
          },
          required: ["name", "appId", "credate"],
        },
      },
    };
  },
  methods: {
    handleImportJson() {
      const t = GenerateSchema.json(JSON.parse(this.importJson));
      delete t.$schema;
      // 保留 name 和 strict 字段
      this.tree = {
        name: this.tree.name || '',
        strict: this.tree.strict || false,
        schema: t
      };
      this.visible = false;
    },
  },
};
</script>
<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
.title {
  text-align: center;
  font-size: 40px;
  font-weight: bold;
  height: 100px;
  line-height: 100px;
}
.version {
  font-size: 16px;
}
.desc {
  padding: 20px;
  width: 80vw;
  min-width: 800px;
  margin: auto;
  padding: 0 3em;
  font-size: 1.2em;
}
.container {
  display: flex;
  padding: 20px;
  width: 80vw;
  min-width: 800px;
  justify-content: center;
  height: calc(100vh - 150px);
  margin: auto;
}
.code-container {
  max-height: 600px;
  overflow: auto;
}
.schema {
  margin-left: 20px;
  width: 50%;
  height: 100%;
  overflow-y: auto;
  overflow-x: hidden;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  padding: 12px;
}
.schema-full {
  width: 100%;
  height: 100%;
  overflow-y: auto;
  overflow-x: hidden;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  padding: 12px;
}
.CodeMirror {
  height: 100% !important;
}
.vue-codemirror {
  flex: 1;
  margin: 0 24px;
  border: 1px solid rgba(0, 0, 0, 0.1);
  min-height: 300px;
  overflow: auto;
  border-radius: 6px;
}
</style>
