# AG Grid 免費版 Row Detail 實現指南

這個專案展示了如何在 AG Grid 免費版中實現 row detail (行詳細資訊) 功能。由於免費版不包含 Master-Detail 功能，我們提供了三種替代方案。

## 📁 檔案說明

- `ag-grid-row-detail.html` - 基礎版本實現
- `ag-grid-advanced-row-detail.html` - 進階版本實現（推薦）
- `README-ag-grid-row-detail.md` - 這個說明文件

## 🚀 三種實現方法

### 方法 1: 展開行 (推薦)
- **原理**: 動態插入詳細資訊行到表格數據中
- **優點**: 
  - 最接近原生 Master-Detail 體驗
  - 詳細資訊直接顯示在表格中
  - 支援動畫效果
- **缺點**: 需要重新設置數據

### 方法 2: 快速檢視
- **原理**: 點擊按鈕顯示簡化的詳細資訊
- **優點**: 
  - 實現簡單
  - 性能好
  - 適合快速預覽
- **缺點**: 資訊顯示有限

### 方法 3: 彈出視窗
- **原理**: 在模態視窗中顯示完整詳細資訊
- **優點**: 
  - 可以顯示大量詳細資訊
  - 不影響表格佈局
  - 使用者體驗佳
- **缺點**: 需要額外的視窗管理

## 💡 核心程式碼解析

### 展開行實現核心程式碼

```javascript
// 生成包含詳細資訊的數據
function generateExpandableData() {
    const expandableData = [];
    originalData.forEach(item => {
        expandableData.push(item);
        
        // 如果該行被展開，添加詳細資訊行
        if (expandedRows.has(item.id)) {
            expandableData.push({
                isDetail: true,
                originalData: item,
                id: `detail-${item.id}`,
                name: createDetailContent(item),
                // 其他欄位設為空
                department: '',
                position: '',
                salary: null,
                expand: ''
            });
        }
    });
    return expandableData;
}

// 切換展開狀態
function toggleRowDetail(id) {
    if (expandedRows.has(id)) {
        expandedRows.delete(id);
    } else {
        expandedRows.add(id);
    }
    
    // 重新設置數據
    gridApi.setRowData(generateExpandableData());
}
```

### 動態行高設定

```javascript
const gridOptions = {
    // ...其他設定
    getRowHeight: params => {
        if (params.data.isDetail) {
            return 'auto'; // 詳細行使用自動高度
        }
        return 50; // 一般行固定高度
    }
};
```

### 自定義 Cell Renderer

```javascript
{
    headerName: '',
    field: 'expand',
    width: 80,
    cellRenderer: params => {
        if (params.data.isDetail) return '';
        
        const isExpanded = expandedRows.has(params.data.id);
        const icon = isExpanded ? 'fa-minus' : 'fa-plus';
        const btnClass = isExpanded ? 'collapse-btn' : 'expand-btn';
        const text = isExpanded ? '收合' : '展開';
        
        return `<button class="${btnClass}" onclick="toggleRowDetail(${params.data.id})">
                    <i class="fas ${icon}"></i> ${text}
                </button>`;
    }
}
```

## 🎨 樣式設計要點

### 詳細面板樣式

```css
.detail-panel {
    background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
    border: 1px solid #dee2e6;
    border-radius: 8px;
    padding: 20px;
    margin: 10px 0;
    animation: fadeIn 0.3s ease-in-out;
}

/* 淡入動畫 */
@keyframes fadeIn {
    from { opacity: 0; transform: translateY(-10px); }
    to { opacity: 1; transform: translateY(0); }
}
```

### 響應式卡片佈局

```css
.detail-content {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 20px;
}

.detail-card {
    background: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    transition: transform 0.2s ease;
}

.detail-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 15px rgba(0,0,0,0.15);
}
```

## 🔧 使用方式

1. **直接開啟 HTML 檔案**
   ```bash
   # 在瀏覽器中開啟
   open ag-grid-advanced-row-detail.html
   ```

2. **整合到現有專案**
   - 複製相關的 CSS 樣式
   - 複製 JavaScript 函數
   - 根據你的數據結構調整程式碼

## 📝 自定義建議

### 修改數據結構
```javascript
// 根據你的需求修改數據結構
const yourData = [
    {
        id: 1,
        // 主要欄位
        mainField1: 'value1',
        mainField2: 'value2',
        
        // 詳細資訊欄位
        detailInfo: {
            contact: { /* 聯絡資訊 */ },
            additional: { /* 額外資訊 */ }
        }
    }
];
```

### 修改詳細面板內容
```javascript
function createDetailContent(data) {
    return `
        <div class="detail-panel">
            <!-- 根據你的需求自定義內容 -->
            <h3>${data.title}</h3>
            <p>${data.description}</p>
            <!-- 更多內容... -->
        </div>
    `;
}
```

## 🌟 功能特色

- ✅ **免費版相容**: 無需付費版 AG Grid
- ✅ **響應式設計**: 支援各種螢幕尺寸
- ✅ **動畫效果**: 流暢的展開/收合動畫
- ✅ **多種方式**: 提供三種不同實現方法
- ✅ **易於自定義**: 可根據需求調整樣式和內容
- ✅ **完整範例**: 包含完整的示例數據和樣式

## 🔍 瀏覽器支援

- Chrome 60+
- Firefox 55+
- Safari 12+
- Edge 79+

## 📞 技術支援

如果您在使用過程中遇到問題，可以：

1. 檢查瀏覽器控制台是否有錯誤訊息
2. 確認 AG Grid CSS 和 JS 檔案載入正常
3. 驗證數據結構是否正確

## 🎯 下一步改進

- [ ] 新增鍵盤快捷鍵支援
- [ ] 實現詳細資訊的編輯功能
- [ ] 加入更多動畫效果
- [ ] 支援巢狀詳細資訊
- [ ] 實現虛擬滾動優化性能