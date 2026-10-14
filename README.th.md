# rigol-dho-mcp

[English](README.md) | **ภาษาไทย**

MCP server สำหรับออสซิลโลสโคป Rigol DHO900 series (ทดสอบกับรุ่น DHO924S) ให้ AI assistant เช่น Claude สั่งงานสโคปผ่าน VISA และสร้างรายงานผลการทดสอบได้

## ความสามารถ
- เชื่อมต่อและดูสถานะ (ค้นหาสโคป Rigol ตัวแรกที่เจอให้อัตโนมัติ)
- ควบคุมการวัด: run, stop, single, autoscale และตั้งค่า channel, timebase, trigger
- ควบคุมเครื่องกำเนิดสัญญาณในตัว (DHO924S)
- จับภาพหน้าจอ อ่านค่าการวัด และบันทึก waveform (CSV พร้อมกราฟ PNG)
- ระบบรายงาน: ทุกภาพและผลวัดจะถูกเพิ่มเข้ารายงานให้อัตโนมัติ แล้ว export เป็น DOCX หรือ HTML ได้

## Tools

### การเชื่อมต่อและสถานะ
| Tool | หน้าที่ |
|---|---|
| `scope_connect` | เชื่อมต่อหรือเชื่อมต่อสโคปใหม่ (ถ้าไม่ระบุ resource จะค้นหาให้อัตโนมัติ) และคืนค่า `*IDN?` |
| `scope_list_resources` | แสดงรายการ VISA resource ที่คอมพิวเตอร์มองเห็น |
| `scope_status` | ดูค่าที่ตั้งไว้ปัจจุบัน: timebase, sample rate, memory depth, trigger และ channel ที่เปิดอยู่ |

### ควบคุมการวัด
| Tool | หน้าที่ |
|---|---|
| `scope_control` | สั่ง `run`, `stop`, `single`, `autoscale`, `force_trigger`, `clear` |
| `scope_wait_trigger` | หลังสั่ง `single` จะรอจนสโคป trigger และหยุดเก็บข้อมูล |
| `configure_channel` | ตั้งค่าการแสดงผล, V/div, offset, coupling (DC/AC/GND), อัตราส่วน probe, BW limit 20 MHz และ label |
| `configure_timebase` | ตั้งค่า s/div, offset แนวนอน, memory depth (1k ถึง 50M), โหมดการเก็บข้อมูล (NORM/PEAK/AVER/ULTR) |
| `configure_trigger_edge` | ตั้งค่า edge trigger: source, level, slope (POS/NEG/RFAL), sweep (AUTO/NORM/SING) |
| `show_measurement_on_screen` | เพิ่มค่าการวัดลงบนหน้าจอสโคป เพื่อให้ติดไปในภาพหน้าจอ |
| `scpi` | ส่งคำสั่ง SCPI ตรง ถ้าเป็นคำสั่ง query จะคืนค่าตอบกลับ ถ้าเป็นคำสั่งอื่นจะคืนค่า `SYST:ERR?` |

### เครื่องกำเนิดสัญญาณในตัว
| Tool | หน้าที่ |
|---|---|
| `generator_status` | อ่านค่าที่ตั้งไว้และสถานะ output ของเครื่องกำเนิดสัญญาณ |
| `configure_generator` | ตั้งค่ารูปคลื่น (SIN/SQU/RAMP/PULS/DC/NOIS/ARB), ความถี่, แอมพลิจูด, offset, duty ของ square และเปิด/ปิด output |

### การบันทึกข้อมูล
| Tool | หน้าที่ |
|---|---|
| `capture_screenshot` | บันทึกภาพหน้าจอเป็น PNG พร้อมค่าการวัดและค่าที่ตั้งไว้ (เลือกได้) และส่งภาพกลับมาให้ดู |
| `get_measurements` | อ่านค่าการวัด (VPP, VMAX, VMIN, VAVG, VRMS, FREQ, PER, RTIM, FTIM, PDUT และอื่น ๆ) ได้หลาย channel |
| `capture_waveform` | บันทึกข้อมูล waveform เป็น CSV พร้อมกราฟ PNG โหมด `NORM` ใช้ข้อมูลบนหน้าจอ ส่วน `RAW` ใช้ข้อมูลเต็มหน่วยความจำ |

### รายงาน
| Tool | หน้าที่ |
|---|---|
| `report_start` | เริ่มรายงานใหม่ (ชื่อรายงาน, DUT, ผู้ทดสอบ, โปรเจกต์) ภาพและผลวัดที่บันทึกหลังจากนี้จะถูกเพิ่มเข้ารายงาน |
| `report_open` / `report_list` | เปิดรายงานเดิมเพื่อทำต่อ / แสดงรายการโฟลเดอร์รายงาน |
| `report_status` | ดูรายงานที่เปิดอยู่และรายการข้อมูลในรายงาน |
| `report_add_note` | เพิ่มข้อความอิสระ เช่น การตั้งค่าการทดสอบ ข้อสังเกต หรือสรุปผล |
| `report_edit_item` | แก้ชื่อ คำอธิบาย ผล PASS/FAIL/INFO หรือ spec limit ของแต่ละรายการ หรือลบรายการ |
| `report_set_info` | แก้ข้อมูลหัวรายงานและสรุปผลรวม |
| `report_export` | สร้างรายงานเป็น DOCX, HTML หรือทั้งสองแบบ |

## ตัวอย่าง prompt
- "เริ่มรายงานทดสอบบอร์ด buck 5V วัด ripple CH1 แบบ AC coupling ที่ 10 µs/div แล้วจับภาพหน้าจอ"
- "เปิดเครื่องกำเนิดสัญญาณ square 1 kHz 3.3 Vpp แล้ววัด rise time ที่ CH1"
- "export รายงานเป็น DOCX"

## การติดตั้ง
```bash
pip install -r requirements.txt
```
ต้องมี VISA backend ด้วย: ติดตั้ง NI-VISA หรือใช้ `pyvisa-py` สำหรับการเชื่อมต่อผ่าน USB/LAN

## การรัน
```bash
python rigol_dho_mcp.py
```

Environment variables:
| ตัวแปร | คำอธิบาย |
|---|---|
| `RIGOL_RESOURCE` | VISA resource string (ค่าเริ่มต้น: ค้นหาสโคป Rigol ตัวแรกให้อัตโนมัติ) |
| `RIGOL_REPORT_DIR` | โฟลเดอร์เก็บรายงาน (ค่าเริ่มต้น: `./reports`) |

## ตั้งค่าใน Claude Code / Claude Desktop
```json
{
  "mcpServers": {
    "rigol-dho": {
      "command": "python",
      "args": ["/path/to/rigol_dho_mcp.py"]
    }
  }
}
```

## License
MIT ดูรายละเอียดที่ [LICENSE](LICENSE)
