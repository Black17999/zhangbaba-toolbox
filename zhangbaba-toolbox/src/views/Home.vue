<template>
  <div>
    <h1>{{ siteName }}</h1>
    <input type="text" v-model="searchTerm" placeholder="搜索工具..." />
    <div v-for="group in filteredGroups" :key="group.id" class="group">
      <h2>{{ group.name }}</h2>
      <ul>
        <li v-for="tool in group.tools" :key="tool.id" @click="selectTool(tool)" style="cursor:pointer; margin-bottom:10px;">
          <strong>{{ tool.name }}</strong><br />
          <small>{{ tool.description }}</small>
        </li>
      </ul>
    </div>
    <hr />
    <div v-if="selectedTool" class="tool-content">
      <h3>{{ selectedTool.name }}</h3>
      <div v-html="selectedTool.htmlContent"></div>
      <button @click="closeTool">关闭工具</button>
    </div>
  </div>
</template>

<script>
import { reactive, computed } from 'vue'

export default {
  setup() {
    const siteName = '张爸爸的工具箱'

    const data = reactive({
      groups: [
        {
          id: 1,
          name: '免费域名',
          tools: [
            {
              id: 11,
              name: '免费域名查询',
              description: '查询免费域名是否可用',
              htmlContent: '<iframe src="https://www.freenom.com/" style="width:100%;height:400px;border:none;"></iframe>'
            }
          ]
        },
        {
          id: 2,
          name: '虚拟主机',
          tools: [
            {
              id: 21,
              name: '主机测速工具',
              description: '测试虚拟主机速度',
              htmlContent: '<p>暂无在线工具，后续更新</p>'
            }
          ]
        },
        {
          id: 3,
          name: '视频API接口',
          tools: [
            {
              id: 31,
              name: '视频转码接口',
              description: '调用视频转码API接口',
              htmlContent: '<p>接口调用示例，需后端支持</p>'
            }
          ]
        },
        {
          id: 4,
          name: '平台',
          tools: []
        },
        {
          id: 5,
          name: 'I0S证书签名',
          tools: []
        },
        {
          id: 6,
          name: 'IP检测',
          tools: [
            {
              id: 61,
              name: 'IP地址检测',
              description: '显示你的公网IP地址',
              htmlContent: '<div>你的IP地址：<span id="ip">检测中...</span></div><script>fetch("https://api.ipify.org?format=json").then(r=>r.json()).then(d=>document.getElementById("ip").textContent=d.ip)</script>'
            }
          ]
        },
        {
          id: 7,
          name: 'Ai生图',
          tools: []
        },
        {
          id: 8,
          name: '购买域名',
          tools: []
        },
        {
          id: 9,
          name: '接码',
          tools: []
        },
        {
          id: 10,
          name: '优选IP',
          tools: []
        }
      ]
    })

    const searchTerm = reactive({ value: '' })
    const selectedTool = reactive({ value: null })

    const filteredGroups = computed(() => {
      if (!searchTerm.value.trim()) {
        return data.groups
      }
      const keyword = searchTerm.value.toLowerCase()
      return data.groups
        .map(g => {
          const filteredTools = g.tools.filter(t => t.name.toLowerCase().includes(keyword) || t.description.toLowerCase().includes(keyword))
          return { ...g, tools: filteredTools }
        })
        .filter(g => g.tools.length > 0)
    })

    function selectTool(tool) {
      selectedTool.value = tool
    }

    function closeTool() {
      selectedTool.value = null
    }

    return { siteName, searchTerm: searchTerm.value, filteredGroups, selectedTool: selectedTool.value, selectTool, closeTool }
  }
}
</script>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 1rem;
}
.group {
  margin-bottom: 2rem;
}
.tool-content {
  border: 1px solid #ccc;
  padding: 1rem;
  margin-top: 1rem;
}
input[type="text"] {
  margin-bottom: 1rem;
  padding: 0.5rem;
  width: 100%;
  max-width: 400px;
  box-sizing: border-box;
}
</style>
