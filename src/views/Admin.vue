<template>
  <div>
    <h1>后台管理 - 张爸爸的工具箱</h1>
    <router-link to="/">返回首页</router-link>

    <section>
      <h2>站点设置</h2>
      <label>
        站点名称：
        <input v-model="siteConfig.name" />
      </label>
      <label>
        关键词：
        <input v-model="siteConfig.keywords" />
      </label>
      <label>
        描述：
        <textarea v-model="siteConfig.description"></textarea>
      </label>
      <button @click="saveSiteConfig">保存设置</button>
    </section>

    <section>
      <h2>工具分组管理</h2>
      <button @click="addGroup">新增分组</button>
      <ul>
        <li v-for="group in groups" :key="group.id">
          <input v-model="group.name" />
          <button @click="removeGroup(group.id)">删除分组</button>
          <button @click="toggleGroupTools(group.id)">
            {{ group.showTools ? '隐藏工具' : '显示工具' }}
          </button>
          <div v-if="group.showTools" style="margin-left: 20px;">
            <button @click="addTool(group.id)">新增工具</button>
            <ul>
              <li v-for="tool in group.tools" :key="tool.id">
                <input v-model="tool.name" placeholder="工具名" />
                <input v-model="tool.description" placeholder="描述" />
                <textarea v-model="tool.htmlContent" placeholder="HTML内容"></textarea>
                <button @click="removeTool(group.id, tool.id)">删除工具</button>
              </li>
            </ul>
          </div>
        </li>
      </ul>
    </section>

    <section>
      <h2>数据管理</h2>
      <button @click="exportData">导出数据（JSON）</button>
      <input type="file" @change="importData" accept=".json" />
    </section>
  </div>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'

const siteConfig = reactive({
  name: '张爸爸的工具箱',
  keywords: '',
  description: ''
})

const groups = ref([])

function loadData() {
  const dataStr = localStorage.getItem('toolboxData')
  if (dataStr) {
    const data = JSON.parse(dataStr)
    if(data.siteConfig) {
      Object.assign(siteConfig, data.siteConfig)
    }
    if(data.groups) {
      groups.value = data.groups.map(g => ({ ...g, showTools: false }))
    }
  } else {
    // 默认数据示例
    groups.value = [
      { id: 1, name: '免费域名', tools: [], showTools: false },
      { id: 2, name: '虚拟主机', tools: [], showTools: false }
    ]
  }
}

function saveData() {
  localStorage.setItem('toolboxData', JSON.stringify({ siteConfig, groups: groups.value }))
}

watch([siteConfig, groups], saveData, { deep: true })

function saveSiteConfig() {
  saveData()
  alert('站点配置已保存')
}

function addGroup() {
  const id = Date.now()
  groups.value.push({ id, name: '新分组', tools: [], showTools: true })
}

function removeGroup(id) {
  if(confirm('确认删除该分组吗？该分组内所有工具也会被删除！')) {
    groups.value = groups.value.filter(g => g.id !== id)
  }
}

function toggleGroupTools(id) {
  const g = groups.value.find(g => g.id === id)
  if(g) g.showTools = !g.showTools
}

function addTool(groupId) {
  const g = groups.value.find(g => g.id === groupId)
  if(g) {
    const id = Date.now()
    g.tools.push({ id, name: '新工具', description: '', htmlContent: '' })
  }
}

function removeTool(groupId, toolId) {
  const g = groups.value.find(g => g.id === groupId)
  if(g) {
    g.tools = g.tools.filter(t => t.id !== toolId)
  }
}

function exportData() {
  const dataStr = JSON.stringify({ siteConfig, groups: groups.value }, null, 2)
  const blob = new Blob([dataStr], { type: 'application/json' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = 'toolbox-data.json'
  a.click()
  URL.revokeObjectURL(url)
}

function importData(event) {
  const file = event.target.files[0]
  if(!file) return
  const reader = new FileReader()
  reader.onload = e => {
    try {
      const data = JSON.parse(e.target.result)
      if(data.siteConfig && data.groups) {
        Object.assign(siteConfig, data.siteConfig)
        groups.value = data.groups.map(g => ({ ...g, showTools: false }))
        alert('数据导入成功')
      } else {
        alert('JSON 数据格式不正确')
      }
    } catch(err) {
      alert('读取JSON失败：' + err.message)
    }
  }
  reader.readAsText(file)
}

// 初始化加载
loadData()
</script>

<style scoped>
input, textarea {
  display: block;
  margin-bottom: 8px;
  padding: 4px;
  width: 100%;
  max-width: 600px;
  box-sizing: border-box;
}
button {
  margin: 4px 8px 8px 0;
}
</style>
