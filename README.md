## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python function for calculating the volume of a cylinder, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

### PROBLEM STATEMENT:
The goal is to design a Python function that calculates the volume of a cylinder and integrate it with a chat-based system powered by a large language model (LLM) that can call the function.

### DESIGN STEPS:
#### STEP 1: Design the Python Function
Create a function calculate_cylinder_volume(radius, height) that takes radius and height as inputs. Use the formula V=πr2hV=πr2h to calculate the volume. Ensure the function handles invalid inputs like zero or negative values.

#### STEP 2: Integrate the Function with the Chat Completion System
Set up a chat-based system that can recognize user queries about cylinder volume. Use an LLM model to prompt the user for radius and height or directly compute the volume if provided. Integrate the volume calculation into the chat flow.

#### STEP 3: User Interaction and Response Handling
Handle user queries by asking for the radius and height if not provided. Compute the volume and return a user-friendly response. Ensure error handling for invalid inputs or missing values.

### PROGRAM:
```py
import math
from typing import Dict, Any
import google.generativeai as genai
import json

# Configuration
GOOGLE_API_KEY = "your-api-key-here"  
genai.configure(api_key=GOOGLE_API_KEY)

def calculate_cylinder_volume(radius: float, height: float) -> float:
    
    return math.pi * (radius ** 2) * height

def create_gemini_prompt(user_input: str) -> str:
    
    return f"""
    Extract the radius and height values from the following query about a cylinder.
    Return only a JSON object with 'radius' and 'height' as keys and their values in meters.
    
    Query: {user_input}
    
    Expected format:
    {{"radius": number, "height": number}}
    """

def parse_gemini_response(response_text: str) -> Dict[str, float]:
    """
    Parse Gemini's response to extract radius and height values.
    
    Args:
        response_text (str): Response from Gemini model
    
    Returns:
        Dict[str, float]: Extracted parameters
    """
    try:
        # Find the JSON object in the response
        start_idx = response_text.find('{')
        end_idx = response_text.rfind('}') + 1
        json_str = response_text[start_idx:end_idx]
        
        # Parse the JSON object
        params = json.loads(json_str)
        
        # Validate required keys
        if 'radius' not in params or 'height' not in params:
            raise ValueError("Missing required parameters in response")
            
        return {
            'radius': float(params['radius']),
            'height': float(params['height'])
        }
    except Exception as e:
        raise ValueError(f"Failed to parse Gemini response: {str(e)}")

def process_user_query(user_input: str) -> Dict[str, Any]:
    
    try:
        # Initialize Gemini model
        model = genai.GenerativeModel('gemini-pro')
        
        # Create and send prompt to Gemini
        prompt = create_gemini_prompt(user_input)
        response = model.generate_content(prompt)
        
        # Parse parameters from response
        parameters = parse_gemini_response(response.text)
        
        # Calculate the volume
        volume = calculate_cylinder_volume(parameters['radius'], parameters['height'])
        
        return {
            "success": True,
            "volume": volume,
            "units": "cubic meters",
            "input_parameters": parameters
        }
    
    except Exception as e:
        return {
            "success": False,
            "error": str(e)
        }

def format_volume(volume: float) -> str:
   
    if volume >= 1:
        return f"{volume:.2f} cubic meters"
    else:
        return f"{volume * 1000:.2f} cubic centimeters"

def main():
    
    print("=== Cylinder Volume Calculator with Gemini Integration ===\n")
    print("Enter 'quit' to exit\n")
    
    while True:
        user_input = input("Enter your query (e.g., 'Calculate volume of cylinder with radius 3m and height 5m'): ")
        
        if user_input.lower() == 'quit':
            break
            
        result = process_user_query(user_input)
        
        if result["success"]:
            volume_str = format_volume(result["volume"])
            print(f"\nResults:")
            print(f"- Volume: {volume_str}")
            print(f"- Dimensions used:")
            print(f"  * Radius: {result['input_parameters']['radius']} meters")
            print(f"  * Height: {result['input_parameters']['height']} meters")
        else:
            print(f"\nError: {result['error']}")
        
        print("\n" + "-" * 50 + "\n")

if __name__ == "__main__":
    main()
```

### OUTPUT:

![389281322-fbf945d4-8788-492e-8358-ffa0d23bc92f](https://github.com/user-attachments/assets/02df347f-b828-4c93-a411-6c842400c3a2)

### RESULT:
Hence,the python program to design and implement a Python function for calculating the volume of a cylinder,
integrating it with a chat completion system utilizing the function-calling feature of a large language model (LLM) is written successfully and executed.
