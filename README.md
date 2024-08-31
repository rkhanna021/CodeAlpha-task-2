import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

class Stock {
    private String symbol;
    private double price;

    public Stock(String symbol, double price) {
        this.symbol = symbol;
        this.price = price;
    }
    public String getSymbol() {
        return symbol;
    }
    public double getPrice() {
        return price;
    }
    public void setPrice(double price) {
        this.price = price;
    }
}

class Portfolio {
    private Map<String, Integer> holdings;
    private double cash;

    public Portfolio(double initialCash) {
        this.holdings = new HashMap<>();
        this.cash = initialCash;
    }

    public void buyStock(String symbol, int quantity, double price) {
        double cost = quantity * price;
        if (cash >= cost) {
            cash -= cost;
            holdings.put(symbol, holdings.getOrDefault(symbol, 0) + quantity);
            System.out.println("Bought " + quantity + " shares of " + symbol + " at $" + price + " per share.");
        } else {
            System.out.println("Insufficient funds to complete the purchase.");
        }
    }

    public void sellStock(String symbol, int quantity, double price) {
        if (holdings.getOrDefault(symbol, 0) >= quantity) {
            cash += quantity * price;
            holdings.put(symbol, holdings.get(symbol) - quantity);
            if (holdings.get(symbol) == 0) {
                holdings.remove(symbol);
            }
            System.out.println("Sold " + quantity + " shares of " + symbol + " at $" + price + " per share.");
        } else {
            System.out.println("You don't own enough shares of " + symbol + " to sell.");
        }
    }

    public double getCash() {
        return cash;
    }

    public double getTotalValue(Map<String, Stock> marketData) {
        double totalValue = cash;
        for (Map.Entry<String, Integer> entry : holdings.entrySet()) {
            String symbol = entry.getKey();
            int quantity = entry.getValue();
            totalValue += quantity * marketData.get(symbol).getPrice();
        }
        return totalValue;
    }

    public void showPortfolio(Map<String, Stock> marketData) {
        System.out.println("Portfolio:");
        for (Map.Entry<String, Integer> entry : holdings.entrySet()) {
            String symbol = entry.getKey();
            int quantity = entry.getValue();
            double price = marketData.get(symbol).getPrice();
            System.out.println(symbol + ": " + quantity + " shares @ $" + price + " each");
        }
        System.out.println("Cash: $" + cash);
        System.out.println("Total Portfolio Value: $" + getTotalValue(marketData));
    }
}

public class StockTradingPlatform {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Map<String, Stock> marketData = new HashMap<>();
        marketData.put("AAPL", new Stock("AAPL", 150.00));
        marketData.put("GOOGL", new Stock("GOOGL", 2800.00));
        marketData.put("TSLA", new Stock("TSLA", 750.00));

        Portfolio portfolio = new Portfolio(10000.00); // Starting with $10,000 cash

        while (true) {
            System.out.println("\nStock Trading Platform");
            System.out.println("1. View Market Data");
            System.out.println("2. Buy Stock");
            System.out.println("3. Sell Stock");
            System.out.println("4. View Portfolio");
            System.out.println("5. Exit");
            System.out.print("Choose an option: ");
            int choice = scanner.nextInt();

            switch (choice) {
                case 1:
                    viewMarketData(marketData);
                   break;
                case 2:
                    System.out.print("Enter stock symbol to buy: ");
                    String buySymbol = scanner.next();
                    if (marketData.containsKey(buySymbol)) {
                        System.out.print("Enter quantity: ");
                       int buyQuantity = scanner.nextInt();
                        portfolio.buyStock(buySymbol, buyQuantity, marketData.get(buySymbol).getPrice());
                    } else {
                        System.out.println("Invalid stock symbol.");
                    }
                    break;
                case 3:
                 System.out.print("Enter stock symbol to sell: ");
                 String sellSymbol = scanner.next();
                    if (marketData.containsKey(sellSymbol)) {
                        System.out.print("Enter quantity: ");
                        int sellQuantity = scanner.nextInt();
                        portfolio.sellStock(sellSymbol, sellQuantity, marketData.get(sellSymbol).getPrice());
                    } else {
                        System.out.println("Invalid stock symbol.");
                    }
                    break;
                case 4:
                    portfolio.showPortfolio(marketData);
                    break;
                case 5:
                    System.out.println("Exiting platform. Thank you!");
                    scanner.close();
                    return;
                default:
                    System.out.println("Invalid option. Please try again.");
            }
        }
    }

    private static void viewMarketData(Map<String, Stock> marketData) {
        System.out.println("\nMarket Data:");
        for (Stock stock : marketData.values()) {
            System.out.println(stock.getSymbol() + ": $" + stock.getPrice());
        }
    }
}
