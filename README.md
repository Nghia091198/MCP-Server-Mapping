# MCP Tools — Hướng dẫn sử dụng & Setup

Tài liệu này mô tả các MCP (Model Context Protocol) tool đang được kết nối với Claude, cách gọi từng tool, và cách setup.

---

## Mục lục

1. [Tổng quan](#tổng-quan)
2. [file-tools](#1-file-tools)
3. [Cách setup](#cách-setup)
4. [Tips sử dụng](#tips-sử-dụng)

---

## Tổng quan

Hiện tại có **2 MCP server** đang kết nối:

| MCP Server | Số tool | Mục đích |
|---|---|---|
| `file-tools` | 8 tools | Đọc/duyệt file, git, quản lý project |
| `Claude in Chrome` | 12 tools | Điều khiển trình duyệt Chrome |

---

## 1. file-tools

Nhóm tool để làm việc với **file hệ thống** và **git** trên máy local.

---

### `list_files` — Liệt kê file trong thư mục

```
list_files <đường_dẫn_thư_mục>
```

**Ví dụ:**
```
list_files D:\BE\omni-web-client\src
```

**Trả về:** Danh sách file và folder con (1 cấp).

---

### `read_file` — Đọc nội dung file

```
read_file <đường_dẫn_file>
```

**Ví dụ:**
```
read_file D:\project\project_detail.js
```

**Trả về:** Toàn bộ nội dung file dưới dạng text.

---

### `search_code` — Tìm keyword trong thư mục

```
search_code <thư_mục> <keyword>
```

**Ví dụ:**
```
search_code D:\project\ isChatOpen
```

**Trả về:** Danh sách file chứa keyword và dòng tương ứng.

---

### `git_status` — Xem file đang thay đổi

```
git_status <đường_dẫn_git_repo>
```

**Ví dụ:**
```
git_status D:\project\asset
```

**Trả về:** Danh sách file theo trạng thái:
- `M` = Modified (đã sửa, đã staged)
- ` M` = Modified (chưa staged)
- `??` = Untracked (file mới chưa được git track)
- `A` = Added (đã staged)
- `D` = Deleted

---

### `git_diff` — Xem nội dung thay đổi

```
git_diff <đường_dẫn_git_repo>
git_diff <đường_dẫn_git_repo> <đường_dẫn_file_cụ_thể>
```

**Ví dụ — xem toàn bộ thay đổi:**
```
git_diff D:\project\code
```

**Ví dụ — xem 1 file cụ thể:**
```
git_diff D:\project\code\layout.html
```

**Trả về:** Diff format chuẩn git (`+` thêm, `-` xóa).

---

### `set_project` — Lưu alias cho project

Thay vì gõ đường dẫn dài mỗi lần, lưu alias 1 lần dùng mãi.

```
set_project <tên_alias> <đường_dẫn>
```

**Ví dụ:**
```
set_project project_1 D:\project_1
set_project project_2 D:\project_2
```

Sau khi lưu, chỉ cần nhắc tên alias, Claude sẽ tự biết đường dẫn.

---

### `get_project` — Lấy đường dẫn theo alias

```
get_project <tên_alias>
```

**Ví dụ:**
```
get_project project_1
```

**Trả về:** Đường dẫn đã lưu tương ứng với alias.

---

### `list_projects` — Xem tất cả project đã lưu

```
list_projects
```

**Trả về:** Danh sách tất cả alias và đường dẫn đã lưu.

**Hiện tại đang có:**
```
• project_1 → D:\project_1
• project_2         → D:\project_1
```

---

## Cách setup

### 1. Setup file-tools (MCP server local)

**Yêu cầu:** Node.js đã cài trên máy.

#### 1.1 Install node

Chạy lệnh

```json
npm install
```

#### 1.2 Config MCP của Claude

Thêm vào file config MCP của Claude (thường ở `claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "file-tools": {
      "command": "npx",
      "args": ["-y", "file-tools-mcp"],
      "env": {}
    }
  }
}
```
**Note:** Nếu file đang có nội dung thì thêm dạng object vào. Không ghi đè

**Vị trí file config:**
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`

Sau khi lưu file config, **restart Claude Desktop** để load MCP server mới.

---

## Tips sử dụng

**Dùng alias thay đường dẫn dài:**
```
# Thay vì gõ:
git_status D:\project_1

# Lưu alias 1 lần:
set_project project_1 D:\project_1

# Sau đó chỉ cần nhắc "project_1", Claude tự hiểu
```

**Review code trước khi commit:**
```
# Xem tổng quan file nào thay đổi
git_status D:\project_1

# Xem chi tiết từng file
git_diff D:\project_1\file.js
```