import json
import boto3
import os
from statistics import mean

# AWS Clients
s3 = boto3.client("s3")
bedrock = boto3.client("bedrock-runtime", region_name="us-east-1")

def lambda_handler(event, context):
    """
    Rift Rewind Codex — Noxian Path
    Lambda function that transforms Riot API match data into Noxian reflections using AWS Bedrock.
    """

    # 1️⃣ Configuration: input data from S3
    bucket = os.environ["S3_BUCKET"]
    key = os.environ["INPUT_KEY"]

    # 2️⃣ Fetch match data from S3
    obj = s3.get_object(Bucket=bucket, Key=key)
    data = json.loads(obj["Body"].read())
    matches = data["matches"]

    # 3️⃣ Compute metrics
    avg_damage = round(mean([m["totalDamageDealtToChampions"] for m in matches]))
    avg_taken  = round(mean([m["totalDamageTaken"] for m in matches]))
    win_rate   = round(sum(1 for m in matches if m["win"]) / len(matches) * 100, 1)

    # 4️⃣ Build Noxian prompt
    prompt = f"""
You are the Noxian Codex. 
Tone: cold, brutal, disciplined. 
Analyze:
Player: {data.get('summoner', 'walirea')} (Talon)
Avg Damage: {avg_damage}
Avg Damage Taken: {avg_taken}
Win Rate: {win_rate}%

Give a short insight (2–3 sentences) and one quote (1 sentence) in pure Noxian style:
no emotion, no metaphor — only function, discipline, control.
Output pure JSON with fields 'insight' and 'quote'.
"""

    # 5️⃣ Invoke Claude 3 Haiku model via Bedrock
    response = bedrock.invoke_model(
        modelId="anthropic.claude-3-haiku-20240307-v1:0",
        body=json.dumps({
            "anthropic_version": "bedrock-2023-05-31",
            "max_tokens": 200,
            "messages": [
                {"role": "user", "content": [{"type": "text", "text": prompt}]}
            ]
        })
    )

    # 6️⃣ Extract Bedrock output
    result = json.loads(response["body"].read())
    output_text = result["content"][0]["text"]

    # 7️⃣ Save results back to S3
    s3.put_object(
        Bucket=bucket,
        Key="output/walirea_noxus.txt",
        Body=output_text.encode("utf-8")
    )

    # 8️⃣ Return metrics summary
    return {
        "status": "ok",
        "saved": "output/walirea_noxus.txt",
        "avg_damage": avg_damage,
        "avg_taken": avg_taken,
        "win_rate": win_rate
    }
