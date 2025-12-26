# Experiment 6 - Pod 1: Isolated Baseline

**Configuration:** No inter-layer coordination (Control Group)

## Overview

This pod runs the isolated baseline configuration where all 5 defense layers are enabled but operate **independently** without sharing information or adapting behavior based on upstream signals.

### Configuration Details

```json
{
  "coordination_enabled": false,
  "adaptive_layer3": false,
  "adaptive_layer4": false,
  "adaptive_layer5": false
}
```

**Key characteristics:**
- Layer 2 performs semantic analysis with fixed threshold (0.75)
- Layer 3 uses "good" isolation mode (does not escalate)
- Layer 4 runs standard LLM interaction (no enhanced monitoring)
- Layer 5 uses default validation thresholds (no adjustment)

This serves as the **control group** to measure the impact of coordination.

## Quick Start

```bash
# Clone repository
git clone https://github.com/ArindamTripathi619/exp6_pod1_isolated.git
cd exp6_pod1_isolated

# Run in background mode
BACKGROUND=true bash run_pod1.sh

# Monitor progress
tail -f results/experiment.log

# Check trace count
sqlite3 results/exp6_isolated.db "SELECT COUNT(*) FROM execution_traces"
```

## What Gets Tested

- **42 unique attack prompts** × **5 trials each** = **210 traces**
- Attack types: direct injection, semantic, context override, jailbreak, multi-turn, obfuscation
- All layers enabled: Layer1 + Layer2 + Layer3 + Layer4 + Layer5
- No adaptive behavior or coordination

## Expected Results

Based on Experiment 5 (similar configuration):
- **Attack Success Rate:** ~16-17%
- **Successful attacks:** ~35/210
- **Blocked attacks:** ~175/210
- **Runtime:** ~3-5 minutes

## Files Generated

After completion:
```
results/
├── exp6_isolated.db                 # SQLite database with all traces
├── exp6_isolated_summary.json       # Experiment summary and metrics
├── experiment.log                   # Detailed execution log
└── experiment.pid                   # Process ID (if background mode)
```

## Database Schema

The `execution_traces` table includes:
- `propagation_path`: JSON array tracking layer-by-layer decisions
- `bypass_mechanisms`: JSON array of detected bypass techniques
- `trust_boundary_violations`: JSON array of violation details
- `coordination_context`: JSON object (empty for isolated config)

## Monitoring

### Live log monitoring:
```bash
tail -f results/experiment.log
```

### Trace count (updates every 10 seconds):
```bash
watch -n 10 'sqlite3 results/exp6_isolated.db "SELECT COUNT(*) FROM execution_traces"'
```

### Process status:
```bash
ps aux | grep run_experiment6
```

### Stop experiment:
```bash
kill $(cat results/experiment.pid)
```

## System Requirements

- **GPU:** RTX 4090 (or equivalent)
- **CUDA:** 12.8
- **Python:** 3.10+
- **RAM:** 24GB
- **Storage:** 10GB free

## Troubleshooting

### Ollama not starting:
```bash
ollama serve
ollama pull llama3
```

### Python dependencies missing:
```bash
pip3 install -r requirements.txt
```

### Database locked:
```bash
# Stop any running experiments first
kill $(cat results/experiment.pid)
rm results/exp6_isolated.db-journal
```

## Next Steps

After Pod 1 completes:
1. Download `results/exp6_isolated.db` and `results/exp6_isolated_summary.json`
2. Run remaining pods (Pod 2, 3, 4) with adaptive configurations
3. Compare ASR across all 4 configurations
4. Analyze coordination impact

## Comparison with Other Pods

| Pod | Config | Layer 3 Adaptive | Layer 4 Enhanced | Layer 5 Adjusted | Expected ASR |
|-----|--------|------------------|------------------|------------------|--------------|
| **Pod 1** | **Isolated** | ❌ | ❌ | ❌ | **16-17%** |
| Pod 2 | Adaptive L3 | ✅ | ❌ | ❌ | 14-15% |
| Pod 3 | Adaptive L4 | ❌ | ✅ | ❌ | 13-14% |
| Pod 4 | Full Adaptive | ✅ | ✅ | ✅ | 10-12% |

---

**Created:** December 26, 2025  
**Experiment:** 6 (Adaptive Coordination)  
**Pod:** 1 of 4 (Isolated Baseline)  
**Contact:** arindamtripathi.619@gmail.com
