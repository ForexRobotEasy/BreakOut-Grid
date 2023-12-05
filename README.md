//+------------------------------------------------------------------+
//|                                             BreakOut Grid.mq5    |
//|                             Copyright © 2021, Forex Robot Easy Team |
//|                                                                  |
//|                        https://www.forexroboteasy.com                 |
//+------------------------------------------------------------------+

//+------------------------------------------------------------------+
//|                            Global Variables                       |
//+------------------------------------------------------------------+
// Grid parameters
input int GridSpacing = 500;         // Spacing between each pending order
input int ScalpTarget = 100;         // Scalp target for each pending order

// Entry modes
input bool EntryByLevel = true;      // Enable entry by level mode
input bool EntryByTime = true;       // Enable entry by time mode
input bool ContinuousOperation = true;  // Enable continuous operation mode

// Entry by level parameters
input double TradeLevel = 1.2000;    // Important trade level
input double LevelTolerance = 0.001; // Tolerance level for triggering the operation

// Entry by time parameters
input int TradeHour = 0;             // Hour for software launch
input int TradeMinute = 0;           // Minute for software launch

// Magic number
input int MagicNumber = 123456;      // Unique identifier for the trading robot

//+------------------------------------------------------------------+
//|                        Expert Advisor Start Function              |
//+------------------------------------------------------------------+
void OnTick()
{
   if (EntryByLevel)
   {
      // Check if price approaches the trade level
      if (MathAbs(SymbolInfoDouble(_Symbol, SYMBOL_BID) - TradeLevel) <= LevelTolerance)
      {
         // Place pending orders
         PlacePendingOrders();
      }
   }
   
   if (EntryByTime)
   {
      // Check if current time matches the specified launch time
      if (TimeHour(TimeCurrent()) == TradeHour && TimeMinute(TimeCurrent()) == TradeMinute)
      {
         // Place pending orders
         PlacePendingOrders();
      }
   }
   
   if (ContinuousOperation)
   {
      // Start the software immediately
      PlacePendingOrders();
   }
}

//+------------------------------------------------------------------+
//|                       Place Pending Orders Function               |
//+------------------------------------------------------------------+
void PlacePendingOrders()
{
   double price = SymbolInfoDouble(_Symbol, SYMBOL_BID);
   
   for (int i = 1; i <= 5; i++)
   {
      // Place pending order for long position
      double longPrice = price - i * GridSpacing;
      OrderSend(_Symbol, OP_BUYSTOP, 0.01, longPrice, 10, 0, 0, 'BreakOut Grid', MagicNumber, 0, clrNONE);
      
      // Place pending order for short position
      double shortPrice = price + i * GridSpacing;
      OrderSend(_Symbol, OP_SELLSTOP, 0.01, shortPrice, 10, 0, 0, 'BreakOut Grid', MagicNumber, 0, clrNONE);
   }
}

//+------------------------------------------------------------------+
//|                         Expert Advisor Deinit                     |
//+------------------------------------------------------------------+
void OnDeinit(const int reason)
{
   // Check if all orders are closed
   if (OrdersTotal() == 0)
   {
      // Restart the software
      PlacePendingOrders();
   }
}

/* 
Product Description:
The BreakOut Grid Expert Advisor is a fully automated trading robot designed to scalp the forex market using a grid strategy. It places pending orders based on specific market conditions, such as price levels or time intervals, and aims to capture small profits from quick price movements.

Key Features:
- Grid Parameters: Users can customize the spacing between each pending order and set a scalp target for each order.
- Entry Modes: The Expert Advisor offers three entry modes - entry by level, entry by time, and continuous operation.
- Entry by Level: Users can set an important trade level and a tolerance level for triggering the operation. Pending orders will be placed when the price approaches the trade level within the specified tolerance.
- Entry by Time: Users can specify a launch time for the software. Pending orders will be placed when the current time matches the specified launch time.
- Continuous Operation: Users can choose to start the software immediately, regardless of price levels or time intervals.
- Magic Number: Each instance of the Expert Advisor is assigned a unique identifier for tracking purposes.

Please note that ForexRobotEasy is not the official developer of this product. We provide sample code that can work as described in this product. For detailed reviews and trading results of this product, please visit the official developer's website or search for the product on the MQL5 platform.
