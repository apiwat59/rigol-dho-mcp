# rigol-dho-mcp

MCP server for the Rigol DHO900 series oscilloscopes (tested on DHO924S). It lets an AI assistant such as Claude control the scope over VISA and build test reports.

## Features
- Connection and status, auto-detecting the first Rigol VISA resource
- Acquisition control: run, stop, single, autoscale, channel, timebase and trigger setup
- Built-in waveform generator control (DHO924S)
- Capture screenshots, measurements and waveform data (CSV plus a plot PNG)
- Report sessions: captures are attached automatically, then exported to DOCX or HTML

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
