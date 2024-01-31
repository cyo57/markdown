# After Effects Expressions Script

#### 边界重复

CC RepeTile

#### 回弹

```javascript
amp = .1; 
freq = 2.0; 
decay = 2.0; 

n = 0; 
if (numKeys > 0){ 
n = nearestKey(time).index;
	if (key(n).time > time){
		n--;
	} 
} 

if(n == 0){
	t = 0;
}else{
	t = time - key(n).time;
} 

if(n > 0){ 
	v = velocityAtTime(key(n).time - thisComp.frameDuration/10); 
	value + v*amp*Math.sin(freq*t*2*Math.PI)/Math.exp(decay*t); 
}else{
	value
}
```

