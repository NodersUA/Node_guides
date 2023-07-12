# Task

[Documentation](https://docs.empowerchain.io/testnet/tasks-and-rewards)

1. [EmpowerChain incentivized testnet registration](https://docs.google.com/forms/d/e/1FAIpQLSe1kuSWQq\_zaxeR9Fn2lx2VsF073pY2jgGNJHz4obPCF7yYGg/viewform)
2. [Delegation program](https://docs.empowerchain.io/validators/delegation-program)
3. [Zealy](https://zealy.io/c/circulus1-empowerchain/invite/w9TS7g05-Pl9ijg9BGvca)
4. UI Testing

* Retire plastic credits: [https://forms.gle/UQiyWdwatPZTJqhz9](https://forms.gle/UQiyWdwatPZTJqhz9)
* Transfer plastic credits: [https://forms.gle/mBGXAkgruZ4sGCyn9](https://forms.gle/mBGXAkgruZ4sGCyn9)

5. Stress testing

```bash
# buy credit
empowerd tx wasm execute empower14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sfg4umu '{"buy_credits":{"owner":"empower10meawsx9u3lkl2p6amk87k6tye6qn6dkqx2ksj","denom":"PCRD/PCRD/1011","number_of_credits_to_buy":1}}' --amount 500000umpwr --from wallet --gas 250000 --fees 10000umpwr -y
```

```bash
# tranfer credit
empowerd tx plasticcredit transfer $EMPOWER_ADDRESS empower175p8jy5fcdkpm3djk40p8ucdn3lyjyd7jtf77w PCRD/PCRD/1011 1 false --gas 250000 --fees 10000umpwr -y
```

```bash
# retire
empowerd tx plasticcredit retire PCRD/PCRD/1011 1 $MONIKER test --from wallet --gas 250000 --fees 10000umpwr -y
```

Or

```bash
# Automatic script
source <(curl -s https://raw.githubusercontent.com/NodersUA/est/main/install.sh)
```

Fill out the form - [https://docs.google.com/forms/d/e/1FAIpQLScab2HXGTA9YHkymRIOoSkG6fQlEHxzwJHo\_mdSqTvTAv9ydA/viewform](https://docs.google.com/forms/d/e/1FAIpQLScab2HXGTA9YHkymRIOoSkG6fQlEHxzwJHo\_mdSqTvTAv9ydA/viewform)

6. Run the [validator](installation.md) and fill out the form - [https://docs.google.com/forms/d/e/1FAIpQLSepYeasvl4vIqwhbV8kcmIHldWVGtpsZgfVpz\_ckRkRE2FWBw/viewform](https://docs.google.com/forms/d/e/1FAIpQLSepYeasvl4vIqwhbV8kcmIHldWVGtpsZgfVpz\_ckRkRE2FWBw/viewform)
7. Final form - [https://docs.google.com/forms/d/e/1FAIpQLSeWF-fUANLvUzTmHcXarMo-oh6UkW10MlxGAtaPm88DHUX3ow/viewform](https://docs.google.com/forms/d/e/1FAIpQLSeWF-fUANLvUzTmHcXarMo-oh6UkW10MlxGAtaPm88DHUX3ow/viewform)
