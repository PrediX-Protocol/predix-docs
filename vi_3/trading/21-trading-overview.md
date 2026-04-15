# 21. Trading Overview

## Hai Venue Giao Dịch
CLOB (Exchange): Sổ lệnh on-chain, limit orders chính xác
AMM (Uniswap v4): Định giá tức thời, thanh khoản thụ động

## Smart Router Kết Hợp Cả Hai
User → Router → CLOB check → AMM check → Split/route tối ưu

## Market Orders vs Limit Orders
Market order: Qua Router, tự động fill tốt nhất
Limit order: Qua Exchange, đặt giá cụ thể, chờ khớp
