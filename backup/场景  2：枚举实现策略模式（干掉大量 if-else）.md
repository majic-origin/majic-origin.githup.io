### 一、本场景涉及的陌生 Java 知识点
1. 枚举中定义抽象方法 abstract
2. 枚举常量后带 {} 代码块
3. 方法重写 @Override 注解
4. @Override 注解的作用与本质
5. 枚举实现策略模式的核心思想
### 二、枚举策略模式 用处
1. 分支逻辑特别多 的场景：支付方式、订单操作、会员等级计算、消息推送类型。
2. 每种类型有独立的业务逻辑，互相不干扰。
3. 想彻底消灭冗长的 if/else if/else。
### 三、优势（重点记）
1. 彻底干掉大量 if/else，代码清爽整洁。
2. 新增业务不用改旧代码：加一个枚举常量、重写方法就行，符合开闭原则。
3. 类型安全：只能用规定好的枚举值，不会传错字符串、数字魔法值。
4. 逻辑收拢：所有分支业务都集中在一个枚举里，好找、好维护。
5. 不用额外建一堆接口、实现类，代码极简。

`public class PayStrategyDemo {

    // 支付策略枚举：每个枚举自己实现一套业务逻辑
    public enum PayStrategy {
        // 每个常量重写抽象方法，各自做自己的逻辑
        ALIPAY {
            @Override
            public void pay(int money) {
                System.out.println("支付宝支付，金额：" + money + " 元");
            }
        },
        WECHAT_PAY {
            @Override
            public void pay(int money) {
                System.out.println("微信支付，金额：" + money + " 元");
            }
        },
        UNION_PAY {
            @Override
            public void pay(int money) {
                System.out.println("银联支付，金额：" + money + " 元");
            }
        };

        // 抽象策略方法：强制每个枚举都必须实现
        public abstract void pay(int money);
    }

    // 测试入口
    public static void main(String[] args) {
        // 直接调用，完全不用 if/else
        PayStrategy.ALIPAY.pay(199);
        PayStrategy.WECHAT_PAY.pay(299);
        PayStrategy.UNION_PAY.pay(399);

        // 模拟业务：根据类型直接匹配策略
        PayStrategy strategy = PayStrategy.WECHAT_PAY;
        strategy.pay(520);
    }
}`