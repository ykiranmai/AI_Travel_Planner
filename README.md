# AI_Travel_Planner
import streamlit as st
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_openai import ChatOpenAI
from langchain.schema import SystemMessage, HumanMessage

# Initialize GoogleAI and OpenAI models
def get_chat_model(api_choice):
    if api_choice == 'GoogleAI':
        return ChatGoogleGenerativeAI(model="gemini-1.5-flash", api_key=st.secrets["general"]["google_ai"])
    elif api_choice == 'OpenAI':
        return ChatOpenAI(model_name="gpt-3.5-turbo", api_key=st.secrets["general"]["openai_api"])

def get_travel_options(source, destination, travel_mode, num_travelers, budget, api_choice):
    chat_model = get_chat_model(api_choice)
    messages = [
        SystemMessage(content="You are an AI-powered Travel Planner. Given the user's input, generate travel choices (cab, train, bus, flights) with estimated costs."),
        HumanMessage(content=f"Provide travel options for traveling from {source} to {destination}. Mode of transport: {travel_mode}. Number of travelers: {num_travelers}. Budget: {budget} INR.")
    ]
    response = chat_model.invoke(messages)
    return response.content

def main():
    st.title("AI Travel Planner")
    st.write("Find the best travel options within your budget using AI-powered recommendations.")
    
    source = st.text_input("Enter your source location:")
    destination = st.text_input("Enter your destination:")
    travel_mode = st.selectbox("Select your preferred mode of transport:", ["Any", "Cab", "Train", "Bus", "Flight"])
    num_travelers = st.number_input("Number of travelers:", min_value=1, step=1)
    budget = st.number_input("Enter your budget (in INR):", min_value=1, step=50)
    api_choice = st.selectbox("Choose API for travel planning", ["GoogleAI", "OpenAI"])
    
    if st.button("Get Travel Options"):
        if source.strip() and destination.strip():
            with st.spinner("Fetching travel options..."):
                options = get_travel_options(source, destination, travel_mode, num_travelers, budget, api_choice)
                st.subheader("Travel Options")
                st.write(options)
        else:
            st.warning("Please enter both source and destination before proceeding.")

if __name__ == "__main__":
    main()
