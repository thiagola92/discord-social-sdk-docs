---
icon: lucide/laugh
---

# The Good, The Bad, The Ugly

### The Bad
4. No signals usage.  
	- I would love to transform some of these callbacks into signals (♥️), but is not possible to identify when can it be done just by looking at functions signature. For example: `void xxxxx(Callback cb);`  
		- If the function name is `set_xxx_callback`: You know that will call you function when "xxx" happens.  
		- If the function name is `do_xxx`: You know that will do "xxx" and call you function when it's done.  
		- The first case can be transformed to signal, while the second can't... Analysing the function name and deciding what to do is a bit too much for me.  