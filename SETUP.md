# Setup Overview

1. AWS Lambda fetches Riot Match-v5 data (KDA, vision, time alive).
2. AWS Bedrock generates text reflections — Ionian (balance) or Noxian (discipline).
3. AWS S3 stores the final Codex text and visual summary.

Each output (insight + quote) was generated with our real match data from EUNE accounts (Walirea & Sylwarina).
Visual integration is built in Figma - full proof of the process is included in the prototype link.
