# WarehouseX-Order-Management-Optimization-Project
using System;
using System.Collections.Generic;
using System.Linq;

namespace WarehouseXIntegrated
{
    // -------------------------
    // Models
    // -------------------------
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Category { get; set; }
        public int Stock { get; set; }
    }

    public class Order
    {
        public int Id { get; set; }
        public int ProductId { get; set; }
        public int Quantity { get; set; }
    }

    // -------------------------
    // Optimization Plan (Activity 1)
    // -------------------------
    public class OptimizationPlan
    {
        public List<string> SqlOptimizations { get; set; } = new List<string>();
        public List<string> ApplicationOptimizations { get; set; } = new List<string>();
        public List<string> DebuggingStrategies { get; set; } = new List<string>();
        public List<string> LongTermStrategies { get; set; } = new List<string>();

        public void PrintPlan()
        {
            Console.WriteLine("=== WarehouseX Optimization Plan ===");
            Console.WriteLine("SQL Optimizations:");
            SqlOptimizations.ForEach(Console.WriteLine);

            Console.WriteLine("\nApplication Optimizations:");
            ApplicationOptimizations.ForEach(Console.WriteLine);

            Console.WriteLine("\nDebugging Strategies:");
            DebuggingStrategies.ForEach(Console.WriteLine);

            Console.WriteLine("\nLong-Term Strategies:");
            LongTermStrategies.ForEach(Console.WriteLine);
        }
    }

    // -------------------------
    // Order Processor with Batch Processing and Robust Debugging (Activities 3 & 4)
    // -------------------------
    public class OrderProcessor
    {
        private readonly List<Product> products;

        public OrderProcessor(List<Product> products) => this.products = products;

        /// <summary>
        /// Process a single order safely.
        /// </summary>
        public void ProcessOrder(Order order)
        {
            try
            {
                if (order == null)
                    throw new ArgumentNullException(nameof(order), "Order cannot be null.");

                var product = products.Find(p => p.Id == order.ProductId);
                if (product == null)
                    throw new InvalidOperationException($"Product with ID {order.ProductId} not found.");

                if (product.Stock < order.Quantity)
                    throw new InvalidOperationException($"Insufficient stock for '{product.Name}'. Requested {order.Quantity}, Available {product.Stock}");

                product.Stock -= order.Quantity;
                Console.WriteLine($"Order {order.Id} processed successfully. Remaining stock for '{product.Name}': {product.Stock}");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error processing order {order?.Id}: {ex.Message}");
            }
        }

        /// <summary>
        /// Batch processing for multiple orders efficiently using a dictionary for fast lookup.
        /// </summary>
        public void ProcessOrders(List<Order> orders)
        {
            if (orders == null || !orders.Any())
            {
                Console.WriteLine("No orders to process.");
                return;
            }

            var productDict = products.ToDictionary(p => p.Id, p => p);

            foreach (var order in orders)
            {
                if (!productDict.TryGetValue(order.ProductId, out var product))
                {
                    Console.WriteLine($"Order {order.Id}: Product not found.");
                    continue;
                }

                try
                {
                    if (product.Stock < order.Quantity)
                    {
                        Console.WriteLine($"Order {order.Id}: Insufficient stock for '{product.Name}'. Requested: {order.Quantity}, Available: {product.Stock}");
                        continue;
                    }

                    product.Stock -= order.Quantity;
                    Console.WriteLine($"Order {order.Id}: '{product.Name}' processed. Remaining stock: {product.Stock}");
                }
                catch (Exception ex)
                {
                    Console.WriteLine($"Error processing order {order.Id}: {ex.Message}");
                }
            }
        }
    }

    // -------------------------
    // SQL-Like Query Optimizer (Activity 2)
    // -------------------------
    public class SqlQueryOptimizer
    {
        private readonly List<Product> products;
        private readonly List<Order> orders;

        public SqlQueryOptimizer(List<Product> products, List<Order> orders)
        {
            this.products = products;
            this.orders = orders;
        }

        /// <summary>
        /// Prints top-selling Electronics products using optimized aggregation.
        /// </summary>
        public void PrintTopSellingElectronics()
        {
            var filteredProducts = products
                .Where(p => p.Category == "Electronics")
                .ToDictionary(p => p.Id, p => p.Name);

            var topSales = orders
                .Where(o => filteredProducts.ContainsKey(o.ProductId))
                .GroupBy(o => o.ProductId)
                .Select(g => new
                {
                    ProductName = filteredProducts[g.Key],
                    TotalSold = g.Sum(o => o.Quantity)
                })
                .OrderByDescending(x => x.TotalSold);

            Console.WriteLine("\n=== Top-Selling Electronics ===");
            foreach (var item in topSales)
            {
                Console.WriteLine($"{item.ProductName}: {item.TotalSold}");
            }
        }
    }

    // -------------------------
    // Program Entry Point
    // -------------------------
    class Program
    {
        static void Main(string[] args)
        {
            // Sample Products
            var products = new List<Product>
            {
                new Product { Id = 1, Name = "Laptop", Category = "Electronics", Stock = 10 },
                new Product { Id = 2, Name = "Headphones", Category = "Electronics", Stock = 20 },
                new Product { Id = 3, Name = "Chair", Category = "Furniture", Stock = 15 }
            };

            // Sample Orders
            var orders = new List<Order>
            {
                new Order { Id = 101, ProductId = 1, Quantity = 5 },
                new Order { Id = 102, ProductId = 2, Quantity = 25 }, // Insufficient stock
                new Order { Id = 103, ProductId = 3, Quantity = 1 },  // Furniture order
                new Order { Id = 104, ProductId = 4, Quantity = 1 }   // Product not found
            };

            // Activity 1: Print Optimization Plan
            var plan = new OptimizationPlan
            {
                SqlOptimizations = new List<string>
                {
                    "Add indexes on Products(Category, ProductID) and Orders(ProductID)",
                    "Refactor queries to use joins efficiently",
                    "Use execution plans to measure improvements"
                },
                ApplicationOptimizations = new List<string>
                {
                    "Reduce redundant database calls",
                    "Optimize loops and I/O operations",
                    "Batch processing for orders"
                },
                DebuggingStrategies = new List<string>
                {
                    "Add null checks for orders and products",
                    "Implement exception handling",
                    "Validate edge cases like insufficient stock"
                },
                LongTermStrategies = new List<string>
                {
                    "Use monitoring tools to track performance",
                    "Schedule periodic query reviews",
                    "Automate optimization tasks using AI tools"
                }
            };

            plan.PrintPlan();

            // Activity 2: SQL-Like Query Optimization
            var sqlOptimizer = new SqlQueryOptimizer(products, orders);
            sqlOptimizer.PrintTopSellingElectronics();

            // Activities 3 & 4: Process Orders Efficiently & Safely
            var processor = new OrderProcessor(products);
            processor.ProcessOrders(orders);
        }
    }
}
