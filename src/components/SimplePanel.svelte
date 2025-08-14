<script>
  // 可选：从父组件传入初始值
  export let initialName = ''
  export let initialItems = []

  //变量
  // 组件内部状态
  let name = initialName
  let count = 0
  let items = [...initialItems]

  // 对外派发事件（可选）
  import { createEventDispatcher } from 'svelte'
  const dispatch = createEventDispatcher()

  function increment() {
    count += 1
    dispatch('countChange', { count })
  }

  function addItem() {
    const v = name.trim()
    if (!v) return
    items = [...items, v]
    dispatch('add', { value: v, items })
    name = ''
  }

  function clearList() {
    items = []
    dispatch('clear')
  }
</script>

<section class="panel">
  <h2>Quick Demo Panel</h2>

  <!-- 输入框和实时显示 -->
  <label class="row">
    <span>你的名字：</span>
    <input placeholder="输入你的名字…" bind:value={name} />
  </label>
  <p class="muted">你好，{name || '匿名用户'} 👋</p>

  <!-- 计数器 -->
  <div class="counter">
    <span>点击次数：<b>{count}</b></span>
    <button on:click={increment}>+1</button>
  </div>

  <!-- 列表 -->
  <div class="list-wrap">
    <div class="ops">
      <button on:click={addItem}>把当前名字加入列表</button>
      <button class="ghost" on:click={clearList}>清空列表</button>
    </div>
    {#if items.length}
      <ul>
        {#each items as item, i}
          <li>{i + 1}. {item}</li>
        {/each}
      </ul>
    {:else}
      <div class="empty">列表为空，先在输入框里输入点内容吧～</div>
    {/if}
  </div>
</section>

<style>
  .panel { padding: 16px; border: 1px solid #e5e7eb; border-radius: 12px; }
  h2 { margin: 0 0 12px; color: #5b8cff; }
  .row { display: grid; grid-template-columns: 96px 1fr; gap: 8px; align-items: center; }
  input {
    padding: 8px; border-radius: 6px; border: 1px solid #d1d5db; outline: none;
  }
  input:focus { border-color: #5b8cff; box-shadow: 0 0 0 3px rgba(91,140,255,.15); }
  .muted { color: #6b7280; margin: 8px 0 12px; }
  .counter { display: flex; align-items: center; gap: 8px; margin: 8px 0 16px; }
  button {
    padding: 8px 14px; border-radius: 8px; border: none; background: #5b8cff; color: #fff; cursor: pointer;
  }
  .ghost { background: transparent; color: #374151; border: 1px solid #d1d5db; }
  .list-wrap { margin-top: 8px; }
  .ops { display: flex; gap: 8px; margin-bottom: 8px; }
  ul { margin: 0; padding-left: 20px; }
  .empty { color: #6b7280; border: 1px dashed #d1d5db; padding: 12px; border-radius: 8px; }
</style>
