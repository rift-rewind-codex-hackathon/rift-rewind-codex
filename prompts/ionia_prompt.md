import json, boto3, os
from statistics import mean

# AWS Clients
s3 = boto3.client("s3")
bedrock = boto3.client("bedrock-runtime", region_name=os.getenv("BEDROCK_REGION", "us-east-1"))

def lambda_handler(event, context):
    """
    Rift Rewind Codex — Ionian Path
    Lambda function that transforms Riot API match data into Ionian reflections using AWS Bedrock.
    """

    # Configuration
    bucket = os.getenv("S3_BUCKET")
    key_in = os.getenv("INPUT_KEY")
    key_out = os.getenv("OUTPUT_KEY", "output/yone_ionia.txt")
    theme = os.getenv("THEME", "IONIA").upper()

    # 1️⃣ Fetch match data from S3
    obj = s3.get_object(Bucket=bucket, Key=key_in)
    data = json.loads(obj["Body"].read())
    matches = data["matches"]

    # 2️⃣ Compute average metrics
    avg_damage = round(mean([m["totalDamageDealtToChampions"] for m in matches]))
    avg_taken  = round(mean([m["totalDamageTaken"] for m in matches]))
    win_rate   = round(sum(1 for m in matches if m["win"]) / len(matches) * 100, 1)

    # 3️⃣ Ionian prompt
    style = (
        "Forget previous context.\n"
        "You are the Ionian Codex. Tone: quiet, reflective, memory-driven, disciplined. "
        "No fanfic, no romance. Calm, concise, metaphor of rhythm/balance."
    )

    ask = (
        f"Player: sylwarina (Yone). Metrics over {len(matches)} games — "
        f"Avg Damage: {avg_damage}, Avg Taken: {avg_taken}, Win Rate: {win_rate}%. "
        "Write a SHORT Ionian insight (2–3 sentences, meditative, about restraint/balance/memory). "
        "Then give ONE Ionian quote (1 sentence). "
        "Return pure JSON with fields 'insight' and 'quote'."
    )

    body = {
        "anthropic_version": "bedrock-2023-05-31",
        "max_tokens": 200,
        "temperature": 0.4,
        "messages": [
            {"role": "user", "content": [{"type": "text", "text": style + '\n' + ask}]}
        ]
    }

    # 4️⃣ Invoke Bedrock (Claude 3 Haiku)
    response = bedrock.invoke_model(
        modelId="anthropic.claude-3-haiku-20240307-v1:0",
        body=json.dumps(body)
    )

    payload = json.loads(response["body"].read())

    # 5️⃣ Extract and parse output
    text = ""
    for block in payload.get("content", []):
        if block.get("type") == "text":
            text += block["text"]

    try:
        parsed = json.loads(text)
    except Exception:
        text_clean = text.strip().strip("`").replace("```json", "").replace("```", "").strip()
        try:
            parsed = json.loads(text_clean)
        except Exception:
            parsed = {"insight": text, "quote": ""}

    # 6️⃣ Save results back to S3 (TXT + JSON)
    out_txt = f"insight: {parsed.get('insight','').strip()}\nquote: {parsed.get('quote','').strip()}\n"

    s3.put_object(Bucket=bucket, Key=key_out, Body=out_txt.encode("utf-8"))
    s3.put_object(
        Bucket=bucket,
        Key=key_out.replace(".txt", ".json"),
        Body=json.dumps(parsed, ensure_ascii=False, indent=2).encode("utf-8")
    )

    return {
        "status": "ok",
        "saved_txt": key_out,
        "avg_damage": avg_damage,
        "avg_taken": avg_taken,
        "win_rate": win_rate
    }
