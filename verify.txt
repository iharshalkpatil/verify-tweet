from transformers import GPT2Tokenizer, GPT2Model
import torch

def generate_tweet_representation(change_description, reason_description):
    # Combine change and reason descriptions
    tweet_content = f"Change: {change_description}\nReason: {reason_description}"

    # Tokenize and encode the tweet
    tokenizer = GPT2Tokenizer.from_pretrained("EleutherAI/gpt-neo-2.7B")
    input_ids = tokenizer.encode(tweet_content, return_tensors="pt")

    # Generate output from the model
    model = GPT2Model.from_pretrained("EleutherAI/gpt-neo-2.7B")
    with torch.no_grad():
        output = model(input_ids)

    # Decode the generated output
    decoded_output = tokenizer.decode(output[0][0], skip_special_tokens=True)
    
    return decoded_output

def verify_tweet(tweet_content, expected_change_description, expected_reason_description):
    # Check if the generated tweet contains the expected change and reason descriptions
    return (
        expected_change_description.lower() in tweet_content.lower() and
        expected_reason_description.lower() in tweet_content.lower()
    )

# Example usage
change_description = "Upgrade component to JDK 17 since Java 11 is going to be out of support."
reason_description = "Hide the create_date column on UI as users are not interested to see when a record is created and also to make some space on the UI apps for other fields to be visible better."

generated_tweet = generate_tweet_representation(change_description, reason_description)

expected_change_description = "Upgrade component to JDK 17"
expected_reason_description = "Java 11 is going to be out of support. Hide the create_date column on UI as users are not interested to see when a record is created and also to make some space on the UI apps for other fields to be visible better."

verification_result = verify_tweet(generated_tweet, expected_change_description, expected_reason_description)

if verification_result:
    print("Tweet content is valid.")
else:
    print("Tweet content is not valid.")


from fastapi import FastAPI, HTTPException

app = FastAPI()

def generate_tweet_representation(change_description, reason_description):
    # The existing function for generating the tweet representation
    # ...

def verify_tweet(tweet_content, expected_change_description, expected_reason_description):
    # The existing function for verifying the tweet content
    # ...

@app.post("/verify_tweet")
async def verify_tweet_api(change_description: str, reason_description: str):
    expected_change_description = "Upgrade component to JDK 17"
    expected_reason_description = "Java 11 is going to be out of support. Hide the create_date column on UI as users are not interested to see when a record is created and also to make some space on the UI apps for other fields to be visible better."

    generated_tweet = generate_tweet_representation(change_description, reason_description)

    verification_result = verify_tweet(generated_tweet, expected_change_description, expected_reason_description)

    if verification_result:
        return {"status": "Tweet content is valid"}
    else:
        raise HTTPException(status_code=400, detail="Tweet content is not valid")

if __name__ == "__main__":
    import uvicorn

    uvicorn.run(app, host="127.0.0.1", port=8000)
