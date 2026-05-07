# MCP Tools — Hướng dẫn sử dụng & Setup

Tài liệu này mô tả các MCP (Model Context Protocol) tool đang được kết nối với Claude, cách gọi từng tool, và cách setup.

---

## Mục lục

1. [Tổng quan](#tổng-quan)
2. [file-tools](#1-file-tools)
3. [Claude in Chrome](#2-claude-in-chrome)
4. [Cách setup](#cách-setup)
5. [Tips sử dụng](#tips-sử-dụng)

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
read_file D:\BE\omni-web-client\src\Haravan.OmniPower.Web\ClientApp\src\App.tsx
```

**Trả về:** Toàn bộ nội dung file dưới dạng text.

---

### `search_code` — Tìm keyword trong thư mục

```
search_code <thư_mục> <keyword>
```

**Ví dụ:**
```
search_code D:\BE\omni-web-client isChatOpen
```

**Trả về:** Danh sách file chứa keyword và dòng tương ứng.

---

### `git_status` — Xem file đang thay đổi

```
git_status <đường_dẫn_git_repo>
```

**Ví dụ:**
```
git_status D:\BE\omni-web-client
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
git_diff D:\BE\omni-web-client
```

**Ví dụ — xem 1 file cụ thể:**
```
git_diff D:\BE\omni-web-client src/Haravan.OmniPower.Web/ClientApp/src/layouts/Layoutv2/index.tsx
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
set_project project BE D:\BE\omni-web-client
set_project landingpage D:\landingpage_builder
```

Sau khi lưu, chỉ cần nhắc tên alias, Claude sẽ tự biết đường dẫn.

---

### `get_project` — Lấy đường dẫn theo alias

```
get_project <tên_alias>
```

**Ví dụ:**
```
get_project project BE
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
• landingpage_builder → D:\landingpage_builder
• project BE         → D:\BE\omni-web-client
```

---

## 2. Claude in Chrome

Nhóm tool để **điều khiển trình duyệt Chrome** thông qua extension. Dùng để tự động hóa thao tác trên web, debug, đọc nội dung trang.

> **Yêu cầu:** Đã cài extension **Claude in Chrome** trên trình duyệt và đang kết nối.

---

### `list_connected_browsers` — Liệt kê trình duyệt đang kết nối

```
list_connected_browsers
```

**Trả về:** Danh sách Chrome instances đang kết nối (deviceId, tên, OS).

---

### `select_browser` — Chọn trình duyệt để thao tác

```
select_browser <deviceId>
```

Dùng sau `list_connected_browsers` để chọn đúng browser muốn điều khiển.

---

### `switch_browser` — Gửi yêu cầu kết nối đến tất cả Chrome

```
switch_browser
```

Gửi popup xác nhận đến tất cả Chrome có extension — người dùng click **Connect** trên browser muốn dùng.

---

### `read_page` — Đọc cây element trên trang

```
read_page <tabId>
read_page <tabId> filter=interactive   # chỉ lấy element tương tác được
```

**Trả về:** Accessibility tree của trang (dạng cây element, có `ref` để dùng với các tool khác).

---

### `get_page_text` — Lấy text thuần từ trang

```
get_page_text <tabId>
```

**Trả về:** Nội dung text của trang, bỏ qua HTML/CSS. Phù hợp để đọc bài viết, tài liệu.

---

### `read_network_requests` — Xem network request

```
read_network_requests <tabId>
read_network_requests <tabId> urlPattern=/api/   # lọc theo URL
```

**Trả về:** Danh sách HTTP request (XHR, Fetch...) trang đang gửi. Dùng để debug API call.

---

### `read_console_messages` — Đọc console log

```
read_console_messages <tabId>
read_console_messages <tabId> onlyErrors=true   # chỉ lấy lỗi
read_console_messages <tabId> pattern=MyApp     # lọc theo pattern
```

**Trả về:** Console log, warning, error từ tab đang mở.

---

### `form_input` — Điền giá trị vào form

```
form_input <tabId> <ref> <value>
```

Dùng `read_page` để lấy `ref` của input, sau đó dùng tool này để điền giá trị.

---

### `file_upload` — Upload file lên trang

```
file_upload <tabId> <ref> <paths>
```

Upload file từ local lên file input trên trang (không dùng click vì sẽ mở dialog native).

---

### `shortcuts_list` — Xem danh sách shortcut/workflow

```
shortcuts_list <tabId>
```

**Trả về:** Danh sách shortcut đã cài trong extension.

---

### `shortcuts_execute` — Chạy shortcut/workflow

```
shortcuts_execute <tabId> command=<tên_lệnh>
```

---

## Cách setup

### 1. Setup file-tools (MCP server local)

**Yêu cầu:** Node.js đã cài trên máy.

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

**Vị trí file config:**
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`

Sau khi lưu file config, **restart Claude Desktop** để load MCP server mới.

---

### 2. Setup Claude in Chrome

1. Cài extension **Claude in Chrome** từ Chrome Web Store
2. Đăng nhập extension bằng tài khoản Claude
3. Mở Claude Desktop → extension sẽ tự động kết nối
4. Dùng `list_connected_browsers` để xác nhận đã kết nối

---

## Tips sử dụng

**Dùng alias thay đường dẫn dài:**
```
# Thay vì gõ:
git_status D:\BE\omni-web-client

# Lưu alias 1 lần:
set_project BE D:\BE\omni-web-client

# Sau đó chỉ cần nhắc "project BE", Claude tự hiểu
```

**Review code trước khi commit:**
```
# Xem tổng quan file nào thay đổi
git_status D:\BE\omni-web-client

# Xem chi tiết từng file
git_diff D:\BE\omni-web-client src/path/to/file.tsx
```

**Tìm nhanh nơi dùng một function/variable:**
```
search_code D:\BE\omni-web-client isChatOpen
```

**Debug web app:**
```
# Bước 1: Xem lỗi console
read_console_messages <tabId> onlyErrors=true

# Bước 2: Xem API call nào đang fail
read_network_requests <tabId> urlPattern=/api/
```
