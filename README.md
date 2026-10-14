# rigol-dho-mcp

**English** | [ภาษาไทย](README.th.md)

MCP server for the Rigol DHO900 series oscilloscopes (tested on DHO924S). It lets an AI assistant such as Claude control the scope over VISA and build test reports.

## Features
- Connection and status, auto-detecting the first Rigol VISA resource
- Acquisition control: run, stop, single, autoscale, channel, timebase and trigger setup
- Built-in waveform generator control (DHO924S)
- Capture screenshots, measurements and waveform data (CSV plus a plot PNG)
- Report sessions: captures are attached automatically, then exported to DOCX or HTML

## Tools

### Connection and status
| Tool | Description |
|---|---|
| `scope_connect` | Connect or reconnect to the scope (auto-detects if no resource is given) and return `*IDN?` |
| `scope_list_resources` | List the VISA resources visible to this PC |
| `scope_status` | Current setup: timebase, sample rate, memory depth, trigger, enabled channels |

### Acquisition control
| Tool | Description |
|---|---|
| `scope_control` | `run`, `stop`, `single`, `autoscale`, `force_trigger`, `clear` |
| `scope_wait_trigger` | After `single`, wait until the acquisition has triggered and stopped |
| `configure_channel` | Display, V/div, offset, coupling (DC/AC/GND), probe ratio, 20 MHz BW limit, label |
| `configure_timebase` | s/div, horizontal offset, memory depth (1k to 50M), acquisition type (NORM/PEAK/AVER/ULTR) |
| `configure_trigger_edge` | Edge trigger: source, level, slope (POS/NEG/RFAL), sweep (AUTO/NORM/SING) |
| `show_measurement_on_screen` | Add a measurement to the scope's on-screen panel so it appears in screenshots |
| `scpi` | Send a raw SCPI command; queries return the response, other commands return `SYST:ERR?` |

### Built-in waveform generator
| Tool | Description |
|---|---|
| `generator_status` | Read the generator settings and output state |
| `configure_generator` | Function (SIN/SQU/RAMP/PULS/DC/NOIS/ARB), frequency, amplitude, offset, square duty, output on/off |

### Capture
| Tool | Description |
|---|---|
| `capture_screenshot` | Save the screen as PNG, optionally with measurements and a setup snapshot, and return the image |
| `get_measurements` | Read measurements (VPP, VMAX, VMIN, VAVG, VRMS, FREQ, PER, RTIM, FTIM, PDUT and more) from one or more channels |
| `capture_waveform` | Save waveform data as CSV plus a plot PNG; `NORM` = screen data, `RAW` = full memory |

### Reports
| Tool | Description |
|---|---|
| `report_start` | Start a report session (title, DUT, engineer, project); later captures are added to it |
| `report_open` / `report_list` | Resume an existing report / list report folders |
| `report_status` | Show the active report and its items |
| `report_add_note` | Add a free-text section (setup, observation, conclusion) |
| `report_edit_item` | Edit an item's label, note, PASS/FAIL/INFO result or spec limit, or delete it |
| `report_set_info` | Update header fields and the overall conclusion |
| `report_export` | Generate the report as DOCX, HTML or both |

## Example prompts
- "Start a report for the 5V buck board, measure CH1 ripple in AC coupling at 10 µs/div and capture a screenshot"
- "Turn on the generator: 1 kHz square, 3.3 Vpp, then measure the rise time on CH1"
- "Export the report as DOCX"

## Install
```bash
pip install -r requirements.txt
```
You also need a VISA backend: NI-VISA, or `pyvisa-py` for USB/LAN.

## Run
```bash
python rigol_dho_mcp.py
```

Environment variables:
| Variable | Description |
|---|---|
| `RIGOL_RESOURCE` | VISA resource string (default: auto-detect the first Rigol) |
| `RIGOL_REPORT_DIR` | Report output folder (default: `./reports`) |

## Claude Code / Claude Desktop config
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
MIT. See [LICENSE](LICENSE).
