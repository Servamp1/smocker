using System;
using System.Collections.Generic;
using System.Threading;

class Program
{
    private static readonly object tableLock = new object();
    private static readonly Random random = new Random();

    private static bool hasTobacco = false;
    private static bool hasPaper = false;
    private static bool hasMatches = false;

    static void Main(string[] args)
    {
        Thread smoker1 = new Thread(Smoker1);
        Thread smoker2 = new Thread(Smoker2);
        Thread smoker3 = new Thread(Smoker3);
        Thread broker = new Thread(Broker);

        smoker1.Start();
        smoker2.Start();
        smoker3.Start();
        broker.Start();

        smoker1.Join();
        smoker2.Join();
        smoker3.Join();
        broker.Join();
    }

    static void Broker()
    {
        while (true)
        {
            lock (tableLock)
            {
                int resource1 = random.Next(3);
                int resource2 = random.Next(3);
                while (resource1 == resource2) 
                {
                    resource2 = random.Next(3);
                }

                if (resource1 == 0) hasTobacco = true; 
                else if (resource1 == 1) hasPaper = true;
                else hasMatches = true; 

                if (resource2 == 0) hasTobacco = true; 
                else if (resource2 == 1) hasPaper = true; 
                else hasMatches = true; 

                Console.WriteLine($"Брокер выставил: {resource1} и {resource2}");
            }

            Thread.Sleep(1000);
        }
    }

    static void Smoker1() 
    {
        while (true)
        {
            lock (tableLock)
            {
                if (hasPaper && hasMatches)
                {
                    Console.WriteLine("Курильщик 1: Курит сигарету.");
                    hasPaper = false;
                    hasMatches = false;
                }
            }
            Thread.Sleep(1000);
        }
    }

    static void Smoker2() 
    {
        while (true)
        {
            lock (tableLock)
            {
                if (hasTobacco && hasMatches)
                {
                    Console.WriteLine("Курильщик 2: Курит сигарету.");
                    hasTobacco = false;
                    hasMatches = false;
                }
            }
            Thread.Sleep(1000); 
        }
    }

    static void Smoker3() 
    {
        while (true)
        {
            lock (tableLock)
            {
                if (hasTobacco && hasPaper)
                {
                    Console.WriteLine("Курильщик 3: Курит сигарету.");
                    hasTobacco = false;
                    hasPaper = false;
                }
            }
            Thread.Sleep(1000); 
        }
    }
}
