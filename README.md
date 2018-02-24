# mxClock
The system clock lifted into the [Matrix](https://github.com/kennytilton/matrix) (data flow system).

## Rationale
Data flow is great, but only to the degree our spreadsheet-like formulae can feed off other data flow-aware information. `mxClock` exists to bring the system clock into the Matrix, starting first with Javascript `setInterval` and `setTimeout`. Here is a Stupid Pet Trick&trade; commented out in the source:

````
var t1 = mkTimer( null, cI(2000), c=> clg(`t1 delay now ${c.md.delay}`), cI(true));

var t2 = mkTimer( null, 2000, function(c) {
    t1.delay = t1.delay - 100;
    t1.onp = ( t1.delay > 0);
});

t2.awaken();
````
The function `mkTimer` looks like this:
````
function mkTimer( owner, delay, func, onp = true, extras) {
    let opts =  Object.assign( { delay: delay, onp: onp}, extras);

    if (func)
        opts.func = func;

    return new mxTimer( owner, opts);
}
````
So `t1` starts out with a delay of 2000ms and has a callback that simply prints out the delay each time it fires. It also starts out turned on. Timer `t2` messes with timer `t1`. It runs every two seconds. Each time it decreases the delay by of `t1` by 100ms. It further turns `t1` off once it is no longer positive.

The astute reader will realize JS intervals' delays cannot be changed. The `mxTimer` logic, when it sees the delay change, discards the JS interval via `clearInterval` and makes a new one with the new delay. 

The above is, as advertised, just a Stupid Pet Trick&trade;. Feel free to RFE more realistic examples.
