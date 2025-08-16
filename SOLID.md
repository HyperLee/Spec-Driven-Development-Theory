---
post_title: SOLID 原則速查
author1: HyperLee
post_slug: solid-principles
microsoft_alias:
featured_image:
categories: [設計原則, 架構]
tags: [SOLID, design, architecture]
ai_note: true
summary: SOLID 五大原則的現代解讀與實務範例（包含 C# 範例），適用於微服務、Clean Architecture 與 AI 輔助開發場景。
post_date: 2025-08-16
---

# SOLID 架構方式（2025 最新視角）

SOLID 是物件導向設計的 5 大原則，由 Robert C. Martin（Uncle Bob）提出。雖然起源於 2000 年代，至今仍是良好架構設計的基石，尤其在微服務、Clean Architecture、DDD（Domain Driven Design）與 AI 輔助開發等場景下應用廣泛。

## 目錄

- [SOLID 架構方式（2025 最新視角）](#solid-架構方式2025-最新視角)
  - [目錄](#目錄)
  - [S — 單一職責原則 (SRP)](#s--單一職責原則-srp)
  - [O — 開放封閉原則 (OCP)](#o--開放封閉原則-ocp)
  - [L — 里氏替換原則 (LSP)](#l--里氏替換原則-lsp)
  - [I — 介面隔離原則 (ISP)](#i--介面隔離原則-isp)
  - [D — 依賴反轉原則 (DIP)](#d--依賴反轉原則-dip)
  - [SOLID 與現代架構的融合](#solid-與現代架構的融合)

---

## S — 單一職責原則 (SRP)

「一個類別只負責一個職責」。核心目標是降低耦合，讓類別的變更理由單一。

2025 實務觀察：在微服務架構中，SRP 不僅適用於類別，也用於「服務」邊界設計；在 AI 輔助開發情境中，單一職責的模組更容易由 AI 生成測試與文件。

範例（C# .NET 8）：

```csharp
// 壞例子：同時處理訂單邏輯 + 寫檔案紀錄
public class OrderService {
    public void PlaceOrder(Order order) {
        // 處理訂單...
    }
    public void SaveLog(string message) {
        File.WriteAllText("log.txt", message);
    }
}

// 好例子：拆分責任
public class OrderService {
    private readonly ILogger _logger;
    public OrderService(ILogger logger) => _logger = logger;

    public void PlaceOrder(Order order) {
        // 處理訂單...
        _logger.Log("Order placed");
    }
}
```

## O — 開放封閉原則 (OCP)

「對擴充開放，對修改封閉」。核心在於能新增功能時不需動到既有程式碼。

實務上常透過介面、抽象層及 DI 容器來達成，對於 plugin-based 系統與 AI 程式碼產生器特別重要。

範例（策略模式）：

```csharp
public interface IPaymentProcessor {
    void Process(decimal amount);
}

public class CreditCardProcessor : IPaymentProcessor {
    public void Process(decimal amount) => Console.WriteLine($"CreditCard: {amount}");
}

public class PayPalProcessor : IPaymentProcessor {
    public void Process(decimal amount) => Console.WriteLine($"PayPal: {amount}");
}

public class PaymentService {
    private readonly IPaymentProcessor _processor;
    public PaymentService(IPaymentProcessor processor) => _processor = processor;

    public void Pay(decimal amount) => _processor.Process(amount);
}
```

## L — 里氏替換原則 (LSP)

「子類別必須能替換父類別，並保持正確性」。繼承應維持行為一致，不能破壞父類別契約。

在微服務 API 設計中，LSP 可延伸為版本相容性（API v2 應相容 v1）。在 AI 生成程式碼情境，LSP 保證自動生成的子類別能安全替換基底類。

違反 LSP 的範例：

```csharp
public class Rectangle {
    public virtual int Width { get; set; }
    public virtual int Height { get; set; }
    public int Area() => Width * Height;
}

public class Square : Rectangle {
    public override int Width {
        set { base.Width = base.Height = value; }
    }
    public override int Height {
        set { base.Width = base.Height = value; }
    }
}
// 使用 Square 會破壞原本 Rectangle 的行為期望
```

## I — 介面隔離原則 (ISP)

「不要強迫使用者依賴不需要的方法」。避免胖介面，將大介面拆成專用的小介面。

在雲端 API 設計，ISP 對應到細粒度 API，減少不必要依賴；在 AI Auto-completion 場景，細小介面讓建議更精準。

範例：

```csharp
// 壞例子：介面太大
public interface IMachine {
    void Print();
    void Scan();
    void Fax();
}

// 好例子：依需求拆分
public interface IPrinter {
    void Print();
}
public interface IScanner {
    void Scan();
}
```

## D — 依賴反轉原則 (DIP)

「高層模組不應依賴低層模組，兩者都應依賴抽象」。透過介面或抽象降低耦合。

在 .NET 8 或 Spring Boot 等框架中，通常透過 DI 容器（IoC Container）實作 DIP；在 AI 驅動程式碼生成情境，DIP 讓自動化測試與模擬更容易。

範例：

```csharp
public interface IMessageSender {
    void Send(string message);
}

public class EmailSender : IMessageSender {
    public void Send(string message) => Console.WriteLine($"Email: {message}");
}

public class NotificationService {
    private readonly IMessageSender _sender;
    public NotificationService(IMessageSender sender) => _sender = sender;

    public void Notify(string msg) => _sender.Send(msg);
}
```

## SOLID 與現代架構的融合

- 微服務：每個服務應遵循 SRP 與 OCP，保持獨立性。
- Clean Architecture：DIP 為核心，使業務邏輯不依賴基礎設施。
- DDD：ISP 與 SRP 有助於維持聚合與限界上下文的清晰。
- AI 輔助開發：清晰的 SOLID 設計可讓 AI 更精準地產生測試、重構與文件。
