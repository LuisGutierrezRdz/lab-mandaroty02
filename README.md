# lab-mandaroty02

## Initial System Code

```
class SystemManager {
    processOrder(order) {
        if (order.type == "standard") {
            verifyInventory(order);
            processStandardPayment(order);
        } else if (order.type == "express") {
            verifyInventory(order);
            processExpressPayment(order, "highPriority");
        }
        updateOrderStatus(order, "processed");
        notifyCustomer(order);
    }

    verifyInventory(order) {
        // Checks inventory levels
        if (inventory < order.quantity) {
            throw new Error("Out of stock");
        }
    }

    processStandardPayment(order) {
        // Handles standard payment processing
        if (paymentService.process(order.amount)) {
            return true;
        } else {
            throw new Error("Payment failed");
        }
    }

    processExpressPayment(order, priority) {
        // Handles express payment processing
        if (expressPaymentService.process(order.amount, priority)) {
            return true;
        } else {
            throw new Error("Express payment failed");
        }
    }

    updateOrderStatus(order, status) {
        // Updates the order status in the database
        database.updateOrderStatus(order.id, status);
    }

    notifyCustomer(order) {
        // Sends an email notification to the customer
        emailService.sendEmail(order.customerEmail, "Your order has been processed.");
    }
}

```

## Lab Exercise Tasks

### Task 1: Analyze the Code

Analizando el código, el primer principio que veo que se está violando es el principio de Single Responsibility Principle (SRP)

### Task 2: Refactor the Code
```
interface InventorySerivce {
  verifyInventory(order);
}

class InventorySerivceImpl implements InventorySerivce {

    verifyInventory(order) {
        // Checks inventory levels
        if (inventory < order.quantity) {
            throw new Error("Out of stock");
        }
    }

}

class OrderProcessor {

    private final InventorySerivce inventorySerivce;
    private final OrderService orderService;
    private final NotificationService notificationService;
    private final Payment payment;

    processOrder(order) {
        inventorySerivce.verifyInventory(order);
        if (order instanceof Standard) {
            // Utilizar factory para obtener StandardPayment
        } else if (order instanceof Express) {
            // Utilizar factory para obtener ExpressPaymen
        }
        orderService.updateOrderStatus(order, "processed");
        notificationService.notifyCustomer(order);
    }
}

interface Payment {
  processPayment(Order, Priority);
}

class ExpressPayment implements Payment {
}

class StandardPayment implements Payment {
}

class Order { }

class Standard extends Order { }

class Express extends Order { }

interface OrderRepository { }

interface OrderService {
  Order updateOrderStatus(Order, status);
}

class OrderServiceImpl implements OrderService {

    private final OrderRepository orderRepository;
    
    Order updateOrderStatus(Order, status) {
      // implementacion utilizando repository
    }
}

interface NotificationService { }

class NotificationServiceImpl implements NotificationService { }

```

### Task 3: Document Your Changes

En el refactor utilizado se empezo por el principio SRP para delegar responsabilidades, ya que el SystemManager realiza distintas cosas por lo que se separo en InventorySerivce y OrderProcessor.

De ahí se genero una clase padre llamada Order la cual se va estender por la ordener Standard y Express, las cuales nos permiten pasar instanacias de estas como si fueran Order.

Se separo en interfaces las logicas de services y sus implementaciones como es el caso de notificaciones, order y payment.
