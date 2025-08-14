<script>
  import { onMount } from 'svelte';

  // 定义卡片数据结构
  let cards = [];
  let nextCardId = 1;
  
  // 添加新卡片
  function addCard(event) {
    // 获取画布位置
    const canvas = document.getElementById('canvas');
    const rect = canvas.getBoundingClientRect();
    
    // 计算相对于画布的点击位置
    const x = event.clientX - rect.left - 150; // 居中显示
    const y = event.clientY - rect.top - 100;  // 居中显示
    
    // 添加新卡片
    cards = [
      ...cards,
      {
        id: nextCardId++,
        title: `卡片 ${nextCardId - 1}`,
        prompt: '请输入提示词...',
        x: Math.max(0, Math.min(x, rect.width - 300)), // 限制在画布内
        y: Math.max(0, Math.min(y, rect.height - 200)), // 限制在画布内
        isDragging: false,
        offsetX: 0,
        offsetY: 0
      }
    ];
  }
  
  // 删除卡片
  function deleteCard(id) {
    cards = cards.filter(card => card.id !== id);
  }
  
  // 开始拖动卡片
  function startDrag(card, event) {
    event.stopPropagation();
    const rect = event.currentTarget.getBoundingClientRect();
    card.isDragging = true;
    card.offsetX = event.clientX - rect.left;
    card.offsetY = event.clientY - rect.top;
  }
  
  // 拖动卡片
  function dragCard(card, event) {
    if (!card.isDragging) return;
    
    const canvas = document.getElementById('canvas');
    const rect = canvas.getBoundingClientRect();
    
    // 计算新位置
    let newX = event.clientX - rect.left - card.offsetX;
    let newY = event.clientY - rect.top - card.offsetY;
    
    // 限制在画布内
    newX = Math.max(0, Math.min(newX, rect.width - 300));
    newY = Math.max(0, Math.min(newY, rect.height - 200));
    
    // 更新位置
    card.x = newX;
    card.y = newY;
  }
  
  // 结束拖动
  function endDrag(card) {
    card.isDragging = false;
  }
  
  // 处理卡片内容更新
  function updateCardField(id, field, value) {
    cards = cards.map(card => 
      card.id === id ? { ...card, [field]: value } : card
    );
  }
  
  // 全局事件监听 - 处理拖动
  onMount(() => {
    function handleMouseMove(event) {
      cards.forEach(card => {
        if (card.isDragging) {
          dragCard(card, event);
        }
      });
    }
    
    function handleMouseUp() {
      cards.forEach(card => {
        if (card.isDragging) {
          endDrag(card);
        }
      });
    }
    
    window.addEventListener('mousemove', handleMouseMove);
    window.addEventListener('mouseup', handleMouseUp);
    
    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
      window.removeEventListener('mouseup', handleMouseUp);
    };
  });
</script>

<div class="app-container">
  <header>
    <h1>画布卡片应用</h1>
    <p>点击画布添加新卡片，拖动卡片调整位置</p>
  </header>
  
  <!-- 画布区域 -->
  <!-- svelte-ignore a11y_click_events_have_key_events -->
  <!-- svelte-ignore a11y_no_static_element_interactions -->
  <div 
    id="canvas" 
    class="canvas"
    on:click={addCard}
  >
    <!-- 卡片列表 -->
    {#each cards as card (card.id)}
      <div 
        class="card"
        style="left: {card.x}px; top: {card.y}px;"
        class:dragging={card.isDragging}
        on:mousedown={(e) => startDrag(card, e)}
      >
        <div class="card-header">
          <input 
            type="text" 
            class="card-title"
            bind:value={card.title}
            on:input={(e) => updateCardField(card.id, 'title', e.target.value)}
            placeholder="标题"
          />
          <!-- svelte-ignore a11y_consider_explicit_label -->
          <button 
            class="delete-btn"
            on:click={(e) => {
              e.stopPropagation();
              deleteCard(card.id);
            }}
          >
            <i class="fas fa-times"></i>
          </button>
        </div>
        
        <div class="card-content">
          <!-- svelte-ignore element_invalid_self_closing_tag -->
          <textarea 
            class="card-prompt"
            bind:value={card.prompt}
            on:input={(e) => updateCardField(card.id, 'prompt', e.target.value)}
            placeholder="请输入提示词..."
          />
        </div>
      </div>
    {/each}
  </div>
</div>

<style>
  .app-container {
    display: flex;
    flex-direction: column;
    height: 100vh;
    overflow: hidden;
  }
  
  header {
    padding: 1rem;
    background-color: var(--bg-color);
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    z-index: 10;
  }
  
  h1 {
    margin: 0;
    color: var(--text-color);
    font-size: 1.5rem;
  }
  
  p {
    margin: 0.5rem 0 0;
    color: var(--text-light);
    font-size: 0.9rem;
  }
  
  .canvas {
    flex: 1;
    position: relative;
    background-color: var(--canvas-bg);
    overflow: auto;
    background-image: 
      linear-gradient(rgba(0, 0, 0, 0.05) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0, 0, 0, 0.05) 1px, transparent 1px);
    background-size: 20px 20px;
  }
  
  .card {
    position: absolute;
    width: 300px;
    background-color: var(--card-bg);
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    transition: box-shadow 0.2s ease, transform 0.2s ease;
    cursor: move;
  }
  
  .card.dragging {
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
    transform: scale(1.02);
    z-index: 100;
  }
  
  .card-header {
    display: flex;
    padding: 0.75rem 1rem;
    background-color: var(--card-header-bg);
    border-bottom: 1px solid var(--border-color);
  }
  
  .card-title {
    flex: 1;
    background: transparent;
    border: none;
    font-size: 1.1rem;
    font-weight: 600;
    color: var(--text-color);
    outline: none;
    padding: 0.25rem 0;
  }
  
  .delete-btn {
    background: transparent;
    border: none;
    color: var(--danger-color);
    cursor: pointer;
    width: 24px;
    height: 24px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background-color 0.2s ease;
  }
  
  .delete-btn:hover {
    background-color: rgba(239, 68, 68, 0.1);
  }
  
  .card-content {
    padding: 1rem;
  }
  
  .card-prompt {
    width: 100%;
    min-height: 100px;
    background: transparent;
    border: none;
    resize: vertical;
    color: var(--text-color);
    font-family: inherit;
    font-size: 0.95rem;
    outline: none;
  }
</style>
