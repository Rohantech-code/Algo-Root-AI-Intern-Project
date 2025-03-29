# Algo-Root-AI-Intern-Project
#First Part
import os
import webbrowser
import psutil

def open_chrome():
    webbrowser.open("https://www.google.com")

def open_calculator():
    os.system("calc")

def get_cpu_usage():
    return f"CPU Usage: {psutil.cpu_percent()}%"
#second part
from sentence_transformers import SentenceTransformer
import faiss
import numpy as np

# Initialize model and database
model = SentenceTransformer('all-MiniLM-L6-v2')
function_descriptions = [
    "Open Google Chrome",
    "Launch Calculator",
    "Get CPU Usage"
]
function_embeddings = model.encode(function_descriptions)
index = faiss.IndexFlatL2(384)
index.add(np.array(function_embeddings))

def retrieve_function(query):
    query_embedding = model.encode([query])
    _, I = index.search(np.array(query_embedding), 1)
    return function_descriptions[I[0][0]]
#Third part
def generate_code(function_name):
    code = f"""
from automation_functions import {function_name}

def main():
    try:
        print({function_name}())
    except Exception as e:
        print(f"Error: {{e}}")

if __name__ == "__main__":
    main()
"""
    return code
import os
import webbrowser
import psutil

# Session Context to store previous actions
session_context = {}

# Automation functions
def open_chrome():
    webbrowser.open("https://www.google.com")
    session_context['last_action'] = 'Opened Chrome'
    return "Chrome opened successfully."

def open_calculator():
    os.system("calc")
    session_context['last_action'] = 'Opened Calculator'
    return "Calculator opened successfully."

def get_cpu_usage():
    cpu_usage = f"CPU Usage: {psutil.cpu_percent()}%"
    session_context['last_action'] = cpu_usage
    return cpu_usage

# Check previous action
def get_last_action():
    return session_context.get('last_action', "No actions performed yet.")
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class RequestBody(BaseModel):
    prompt: str

@app.post("/execute")
async def execute_function(request: RequestBody):
    try:
        function_name = retrieve_function(request.prompt)
        code = generate_code(function_name)
        return {"function": function_name, "code": code}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
