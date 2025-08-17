# us-stock-analysis

from fastapi import FastAPI
import yfinance as yf

# Create FastAPI app
app = FastAPI(
    title="US Stock Price API",
    description="Get real-time US stock prices using Yahoo Finance data.",
    version="1.0.0"
)

@app.get("/")
def root():
    """Root endpoint with a welcome message."""
    return {"message": "Welcome to the US Stock Price API! Use /price/{ticker} to get stock data."}


@app.get("/price/{ticker}")
def get_stock_price(ticker: str):
    """
    Fetch the latest stock price for a given US ticker symbol.
    Example: /price/AAPL
    """
    try:
        stock = yf.Ticker(ticker)
        data = stock.history(period="1d")

        if data.empty:
            return {"error": f"No data found for ticker {ticker}"}

        last_quote = data["Close"].iloc[-1]
        return {
            "ticker": ticker.upper(),
            "last_price": round(float(last_quote), 2)
        }
    except Exception as e:
        return {"error": str(e)}
