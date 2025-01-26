
Rate Limiter Library Documentation
Overview
The RateLimiter library provides a flexible and extensible way to control the rate of requests for specific identifiers, such as IP addresses or tokens. The library supports several rules for rate limiting, including fixed windows, timespan-based limits, and composite rules (AND, OR). This allows for the application of complex rate-limiting strategies to prevent abuse and ensure fairness.

The library consists of several components:

Identifiers – Represent the entities whose rate of requests is being limited (e.g., IP address, token).
Rules – Define the conditions under which requests are allowed or denied, including FixedWindow, Timespan, and logical combinations like And and Or.
History – Keeps track of requests and allows checking whether a request exceeds the limits within a specific timeframe.

Usage Example
Here’s an example of how to use the RateLimiter library to apply a fixed window rule to limit requests from an IP address.

Step 1: Set up the history
First, you need to implement a class for storing request history (e.g., in-memory storage or a database). Here is a simple example using an in-memory collection:


public class InMemoryFixedWindowHistory : IFixedWindowHistory
{
    private readonly Dictionary<string, List<DateTime>> _requestHistory = new();

    public int GetRequestCount(IIdentifier identifier, DateTime start, DateTime end)
    {
        if (_requestHistory.TryGetValue(identifier.ToString(), out var requests))
        {
            return requests.Count(r => r >= start && r <= end);
        }
        return 0;
    }

    public void Record(IIdentifier identifier, DateTime now)
    {
        if (!_requestHistory.ContainsKey(identifier.ToString()))
        {
            _requestHistory[identifier.ToString()] = new List<DateTime>();
        }

        _requestHistory[identifier.ToString()].Add(now);
    }
}
Step 2: Create a rule
Create a FixedWindow rule to limit the number of requests:


var history = new InMemoryFixedWindowHistory();

var fixedWindowRule = new FixedWindow(history, maxCount: 5, window: 10); // Max 5 requests in 10 seconds



Step 3: Check if requests are allowed
Now, you can create an identifier (e.g., IP address) and check if requests are allowed:


var ipAddress = new IpAddress("192.168.1.1");

for (int i = 0; i < 6; i++)
{
    bool isAllowed = fixedWindowRule.Check(ipAddress);
    Console.WriteLine(isAllowed ? "Request allowed" : "Request denied");

    // Simulate a delay between requests
    Task.Delay(1000).Wait();
}
In this example, the rule allows a maximum of 5 requests in 10 seconds. The 6th request will be denied.

Conclusion
The RateLimiter library is a powerful tool for controlling request rates based on different conditions. By combining various rules (such as FixedWindow, Timespan, and logical combinations like And and Or), you can build sophisticated rate-limiting strategies tailored to your application’s needs.
