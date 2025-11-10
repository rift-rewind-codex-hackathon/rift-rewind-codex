# rift-rewind-codex
AWS + Riot Games Hackathon project: transforms Riot API data into Noxian and Ionian stories.
Rift Rewind Codex — Dual Path: Noxus & Ionia
This project was created for the AWS + Riot Games Hackathon 2025.
It’s not a full web app: it’s an experimental, artistic concept that blends data, storytelling, and League of Legends lore.
We’re two sisters, both artists.
Walirea and Sylwarina work together as concept artist, story writer, and graphic designer.
We wanted to see what happens when art and data meet, when stats from League can turn into a story told in the voice of a region.
Rift Rewind Codex takes real Riot API match data and transforms it into Ionian or Noxian reflections using AWS services.
The Codex uses our own real match history (walirea & sylwarina on EUNE) — turning our actual gameplay into narrative reflections.
It’s not about analytics; it’s about emotion and identity through playstyle.

How it works
AWS Lambda fetches and processes Riot API data (KDA, damage, vision, time alive).
AWS Bedrock generates text reflections: insights and quotes written in Ionian or Noxian style.
AWS S3 stores the generated Codex cards and visual outputs.
All UI and visual design were created from scratch in Figma.
Screenshots from AWS (Lambda setup, Bedrock outputs, S3 process) and the final Codex visuals can be found inside our Figma prototype: they serve as proof of how the project was built and tested.

Each Codex interprets the same data in two philosophies:
Noxus: Discipline and precision : “Control wins before power does.”
Ionia: Awreness and balance : “The wind remembers every name it cuts.”

Project links
- Figma prototype: https://www.figma.com/design/QLwnPIw0GQBcnKSwDufx3U/Rift-Rewind-Codex-%E2%80%94-Dual-Path--Noxus---Ionia?node-id=0-1&p=f&t=Ir7oHCuDBADvilCc-0
- Hackathon submission: Rift Rewind Codex (Dual Path: Noxus & Ionia)
-This repo: Screenshots and proofs from AWS (Lambda setup, Bedrock outputs, S3 storage)

Team
Walirea & Sylwarina - sisters, concept artists, story writers, and designers.
We main Talon and Yone, and this project grew from that - Noxus and Ionia, discipline and reflection.
We’re not professional developers; we learned everything during this hackathon.
ChatGPT helped us understand some Python and JavaScript basics and guided us through AWS setup.
This was an experiment: equal parts creative and technical - to see if gameplay data could feel like a personal story.

License
MIT License - free to view, learn from, or adapt.

