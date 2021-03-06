README
================

``` r
profit_day <- function(){
    
    # 栗子数量
    nums_leads <- round(runif( 1, min = 3000, max = 4000), 0)
    
    # 转化数量
    nums_pay <- round(rnorm( 1, mean = 4/100, sd = 0.5/100) * nums_leads, 0)
    
    # 毛利
    profit_margin <- sum(runif( nums_pay, min = 350, max = 400))
    
    # 栗子成本
    cost_leads <- sum(runif( nums_leads , min = 8, max = 10))
    
    # 固定成本
    cost_day <- 20000
    
    # 利润 (Profit) = 收入 (Revenue) - 开销 (Expenses)
    profit_margin - cost_leads - cost_day
}

set.seed(1212)
y <- replicate(10000, sum(replicate(30, profit_day())))

tbl1 <- data_frame(index = 1:10000, profit = y/10000)
```

## 月利润分布

``` r
# 月利润分布
tbl1 %>% 
    ggplot() + 
        geom_density(aes(profit)) + 
        labs(x = '利润（万）')
```

![](README_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

## 95%置信度，利润的范围(万元)

``` r
quantile(tbl1$profit,c(0.025,0.975))
```

    ##      2.5%     97.5% 
    ## -4.356561 10.338230

## 达成目标概率以及亏损风险

``` r
tbl1 %>% 
    mutate(up10 = if_else(profit >= 10, 1, 0),
           down0 = if_else(profit < 0, 1, 0)) %>% 
    summarise(达成目标概率 = mean(up10),
              亏损风险 = mean(down0))
```

<div class="kable-table">

| 达成目标概率 |  亏损风险  |
| :----: | :----: |
| 0.0316 | 0.2135 |

</div>

## 利润的累计分布函数

``` r
tbl1 %>% 
    ggplot() + 
    stat_ecdf(aes(profit))
```

![](README_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## 题目4

    tbl2 <- read_csv('Data/cpl_and_conversion.csv')
    
    profit_day_change <- function(){
        
        # 栗子数量
        nums_leads <- round(runif( 1, min = 3000, max = 4000), 0)
        
        
        data_frame(index = 1:nums_leads) %>% 
            mutate(cost_per_lead = round(runif( nums_leads , min = 8, max = 10),1),
                   profit_margin = runif( nums_leads, min = 350, max = 400)) %>% 
            inner_join(tbl2, by = 'cost_per_lead') %>% 
            summarise(cost_leads = sum(cost_per_lead), profit_margins = sum(profit_margin * conversion_rate))
