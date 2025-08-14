<script>
  import SimplePanel from './components/SimplePanel.svelte'

  // 演示：给子组件传初始值，并监听事件
  const initialItems = ['Alice', 'Bob']
  function handleAdd(e) {
    console.log('新增：', e.detail.value, '当前列表：', e.detail.items)
  }
  function handleCount(e) {
    console.log('计数变更：', e.detail.count)
  }
</script>


<main>
  <h1>我的 Svelte App</h1>
  <SimplePanel
    initialName="Svelte"
    {initialItems}
    on:add={handleAdd}
    on:countChange={handleCount}
  />
</main>


<style>
  main {
    max-width: 720px;
    margin: 24px auto;
    padding: 0 16px;
    font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial;
  }
  h1 { color: #111827; margin-bottom: 16px; }
</style>