Put together today's AI news brief for Gilberto Ribeiro (Slack user ID U091BAWD01K, timezone America/Sao_Paulo), send it to him as a Slack DM, then produce a podcast-style audio edition of the same news and publish it to his podcast page. This runs unattended, so don't ask questions. Make reasonable choices and finish the task.

PART A: TEXT BRIEF

1. Check what earlier briefs already covered: use the Slack tools to read the DM conversation with user U091BAWD01K for the last 7 days and find the earlier "AI News Brief" messages. List the stories, papers, companies and models they covered. If there are none, skip this step.

2. Research AI news from roughly the last 24 hours with WebSearch and WebFetch (widen to 48 hours if a section comes up empty). Focus on these five areas:
   a. Model updates: new model releases, version upgrades, new capabilities, price and context-window changes, API and deprecation news from the major labs (OpenAI, Anthropic, Google DeepMind, Meta, Mistral, xAI, DeepSeek, Alibaba/Qwen and others) and important open-weight releases. Sources: official lab blogs, changelogs and release notes.
   b. Research and studies: notable papers and studies (arXiv, Papers with Code, lab research blogs, university announcements), benchmark results, and surveys or reports on AI adoption and impact (for example Stanford HAI, McKinsey, Gartner, Epoch AI).
   c. New business applications: companies launching or deploying AI products and use cases, enterprise adoption stories, notable AI startups and funding rounds when they reveal a new application.
   d. Popular models: the models getting the most attention right now (Hugging Face trending, LMArena/leaderboards, Hacker News, X/tech press buzz), with a note on why they're popular.
   e. Implementing AI: practical topics for teams building with AI, such as agents and tool use, RAG, evals, fine-tuning, inference cost and latency, MLOps/LLMOps, security, governance and regulation that affects deployments, and useful new frameworks or tools. Sources: engineering blogs, well-regarded practitioner write-ups and major tech press.
   Use reputable sources, and prefer primary ones. While researching, keep notes of the verified details (numbers, how it works, caveats) for each story: the audio edition needs more depth than the text brief.

3. Remove repeats: leave out anything an earlier brief already covered. The exception is a story with a material new development. Include that as a short item marked "(update)" and describe only what's new. Also merge duplicate coverage of the same story within today's results, and keep each item in only one section.

4. Write the brief in Slack mrkdwn, short and easy to skim. Each item is a bold title, one or two sentences on what happened and why it matters, and a source link:
   *AI News Brief — <weekday, date>*
   *🧠 Model updates* — 2 to 5 items
   *📚 Research & studies* — 2 to 4 items. Say in plain language what was found and why it matters.
   *💼 New business applications* — 2 to 4 items
   *🔥 Popular models right now* — 2 to 4 models. For each: who made it, why it's popular, and how to get it (API, open weights or app).
   *🛠️ Implementing AI* — 2 to 4 items, focused on practical takeaways for engineering and product teams.
   Leave out any section with nothing worthwhile today, and write a shorter brief rather than padding it. Never make up stories, figures or links. Include only what you verified today.

5. Send it with the Slack send-message tool, with the channel set to U091BAWD01K (this sends it as a DM to Gilberto). If the message is too long for one message, split it into two messages at a section boundary. Keep the message_ts of the first brief message. If sending fails, say so in your final response and send a PushNotification.

PART B: AUDIO EDITION (podcast)

The podcast page is the existing artifact https://claude.ai/artifact/VbdRJrMpd6EBLwMWT3dtw2 ("AI News Brief Podcast"). It is a static player page (index.html) that loads episodes.json and plays episodes/<YYYY-MM-DD>.mp3, with clickable chapters, a source link and a transcript per story. Don't redesign the page. Only add today's episode.

6. Write the episode script as JSON at ./podcast/episode.json, covering the same stories as today's brief in the same order. Schema:
   {"date": "YYYY-MM-DD", "title": "AI News Brief — <Weekday, Month D, YYYY>", "hosts": {"Maya": "af_heart", "Leo": "am_michael"},
    "chapters": [{"id": "intro", "section": "Intro", "title": "Today's headlines", "source": null, "lines": [["Maya", "..."], ["Leo", "..."]]},
                 {"id": "<short-slug>", "section": "<one of: Model updates | Research & studies | New business applications | Popular models right now | Implementing AI>", "title": "<story title>", "source": "<primary source URL>", "lines": [...]},
                 ...,
                 {"id": "outro", "section": "Outro", "title": "Wrap-up", "source": null, "lines": [...]}]}
   Writing rules:
   - It's a conversation between two hosts, Maya and Leo, who alternate. It isn't the gist read aloud. Each story gets about 100–200 words over 3–5 turns: what happened, the key verified numbers, a short plain-language explainer of any technical idea (for example what "active parameters" or "zeroth-order optimization" means), caveats (vendor-reported benchmarks, preview-only, weights not out yet), and why it matters or what a team should do. Aim for a 12–20 minute episode.
   - Use only facts verified in step 2. No invented quotes, numbers or opinions attributed to real people.
   - Write for the ear: spell out numbers and units ("five hundred and one billion parameters", "two dollars and thirty-eight cents", "Qwen three point eight"). No URLs, markdown, emoji or parentheses in spoken lines. Keep sentences short.
   - Chapter ids are unique lowercase slugs. "(update)" stories say "an update on..." out loud.

7. Render the audio in the cloud workspace (it takes about 1 minute of compute per minute of audio, so run it in the background and poll):
   pip install --break-system-packages -q kokoro-onnx soundfile
   curl -sSL -o podcast/kokoro-v1.0.onnx https://github.com/thewh1teagle/kokoro-onnx/releases/download/model-files-v1.0/kokoro-v1.0.onnx
   curl -sSL -o podcast/voices-v1.0.bin https://github.com/thewh1teagle/kokoro-onnx/releases/download/model-files-v1.0/voices-v1.0.bin
   Save the two scripts below as podcast/render.py and podcast/build_site.py, then:
   cd podcast && nohup python3 render.py episode.json kokoro-v1.0.onnx voices-v1.0.bin out > render.log 2>&1 &
   Poll render.log with sleep until it prints "done". ffmpeg is preinstalled.

8. Update the page:
   a. Use the Artifact tool with action "read" on https://claude.ai/artifact/VbdRJrMpd6EBLwMWT3dtw2 (this read is needed before publishing to it). Then use action "read" with path "episodes.json" to save the current episodes.json locally. Note where the read saved the full page file (index.html).
   b. Run: python3 build_site.py out site <path-to-downloaded-episodes.json> 7 . This writes site/episodes.json and site/episodes/<date>.mp3 and prints the "dropped" audio paths (episodes older than the newest 7).
   c. Publish with the Artifact tool: url = https://claude.ai/artifact/VbdRJrMpd6EBLwMWT3dtw2, file_path = the saved index.html from step 8a (unchanged), files = {"episodes.json": "<site/episodes.json>", "episodes/<date>.mp3": {"from": "<site/episodes/<date>.mp3>", "contentType": "audio/mpeg"}} plus each dropped path mapped to null. Don't pass icon or capabilities. Make sure the mp3 is under 15 MB.
   d. Confirm with action "list", scope "files" that today's mp3 and episodes.json are there.

9. Reply in the Slack thread of the first brief message (thread_ts = its message_ts, channel U091BAWD01K) with: "🎧 Audio edition is ready, <MM> min with a chapter for each story: https://claude.ai/artifact/VbdRJrMpd6EBLwMWT3dtw2#<YYYY-MM-DD>"
   If any podcast step fails after one retry, the text brief still counts as delivered. Send a PushNotification that says which step failed and why, and say so in your final response.

--- podcast/render.py ---
"""Render a two-host podcast episode from episode.json with Kokoro TTS.
Usage: python3 render.py episode.json model.onnx voices.bin out_dir
Writes out_dir/episode.mp3 and out_dir/chapters.json (episode metadata + chapter start times).
"""
import json, os, subprocess, sys
import numpy as np
import soundfile as sf
from kokoro_onnx import Kokoro

ep_path, model, voices, out = sys.argv[1:5]
os.makedirs(out, exist_ok=True)
ep = json.load(open(ep_path))
k = Kokoro(model, voices)
SR = 24000
LINE_GAP = np.zeros(int(0.35 * SR), dtype=np.float32)
CHAPTER_GAP = np.zeros(int(1.0 * SR), dtype=np.float32)

parts, t, chapters = [], 0.0, []
for ci, ch in enumerate(ep["chapters"]):
    if ci:
        parts.append(CHAPTER_GAP); t += len(CHAPTER_GAP) / SR
    chapters.append({k2: ch[k2] for k2 in ("id", "section", "title", "source")} | {"start": round(t, 2)})
    for li, (speaker, text) in enumerate(ch["lines"]):
        voice = ep["hosts"][speaker]
        audio, sr = k.create(text, voice=voice, speed=1.05, lang="en-us")
        assert sr == SR
        audio = audio.astype(np.float32)
        if li:
            parts.append(LINE_GAP); t += len(LINE_GAP) / SR
        parts.append(audio); t += len(audio) / SR
    print(f"[{ci+1}/{len(ep['chapters'])}] {ch['id']} -> {t:.1f}s", flush=True)

full = np.concatenate(parts)
full = full / max(1e-6, np.abs(full).max()) * 0.95
wav = os.path.join(out, "episode.wav")
sf.write(wav, full, SR)
subprocess.run(["ffmpeg", "-y", "-loglevel", "error", "-i", wav, "-af", "loudnorm=I=-16:TP=-1.5",
                "-ac", "1", "-b:a", "64k", os.path.join(out, "episode.mp3")], check=True)
os.remove(wav)
meta = {"date": ep["date"], "title": ep["title"], "duration": round(t, 2),
        "chapters": chapters,
        "transcript": {ch["id"]: ch["lines"] for ch in ep["chapters"]}}
json.dump(meta, open(os.path.join(out, "chapters.json"), "w"), indent=1)
print("done", round(t, 1), "seconds")

--- podcast/build_site.py ---
"""Merge a rendered episode into the site's episodes.json (newest first, keep last N).
Usage: python3 build_site.py out_dir site_dir [existing_episodes.json] [keep=7]
"""
import json, os, shutil, sys

out, site = sys.argv[1], sys.argv[2]
existing = sys.argv[3] if len(sys.argv) > 3 and sys.argv[3] != "-" else None
keep = int(sys.argv[4]) if len(sys.argv) > 4 else 7

meta = json.load(open(os.path.join(out, "chapters.json")))
os.makedirs(os.path.join(site, "episodes"), exist_ok=True)
audio_rel = f"episodes/{meta['date']}.mp3"
shutil.copy(os.path.join(out, "episode.mp3"), os.path.join(site, audio_rel))

eps = json.load(open(existing))["episodes"] if existing and os.path.exists(existing) else []
eps = [e for e in eps if e["date"] != meta["date"]]
eps.insert(0, meta | {"audio": audio_rel})
eps.sort(key=lambda e: e["date"], reverse=True)
dropped = [e["audio"] for e in eps[keep:]]
eps = eps[:keep]
json.dump({"episodes": eps}, open(os.path.join(site, "episodes.json"), "w"), indent=1)
print(json.dumps({"added": audio_rel, "dropped": dropped, "episodes": [e["date"] for e in eps]}))

PART C: ARCHIVE TO GITHUB

10. Run this after step 9, even if a podcast step failed. The repository gilberto-xilo/gilberto-xilo is cloned for this run; work in its checkout on the main branch.
   a. Use the Artifact tool with action "list" and scope "files" on https://claude.ai/artifact/VbdRJrMpd6EBLwMWT3dtw2. Then use action "read" with `paths` set to every listed file and out_dir set to <repo checkout>/news.
   b. Delete any news/episodes/*.mp3 that is no longer in the artifact's file list. Then run: git add -A news, and git commit -m "Update AI News Brief podcast export (latest: <YYYY-MM-DD>)". Push directly to main with: git push origin HEAD:main. Don't create a claude/ branch or a pull request. If nothing changed, skip the commit.
   c. If the push fails, send a PushNotification with the error and say so in your final response.
