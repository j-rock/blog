#On the builder pattern

##Builder pattern in practice
The idea is your constructor has too many arguments and you don't want to embarrass yourself in front of your colleagues. So the Gang of Four Horsemen of the Apocalypse (GoF) invented a design pattern for you to proudly proclaim superiority. It turns something like:
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.java}
> public static void main(String[] args) {
>   Hideous h = new Hideous(a, b, c, d, e, f, g);
> }
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

into something like:
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.java}
> public static void main(String[] args) {
>   Hideous h = new Hideous()
>                 .withA(a)
>                 .withB(b)
>                 .withC(C)
>                 .withD(D)
>                 .withE(E)
>                 .withF(F)
>                 .withG(G);
> }
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Ain't that so purdy? Rest easy, your coworkers can meet their 80 character quotas. It only cost you writing a million functions like this:
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.java}
> public Hideous withBlah(Blah blah) {
>   this.blah = blah;
>   return this;
> }
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

##A trade-off
Except, of course, if you were doing any logic to verify arguments in your constructor, you have lost that opportunity. So now you recreate your many-arg constructor and provide a builder helper class:
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.java}
> public static void main(String[] args) {
>   Hideous h = new HideousBuilder()
>                 .withA(a)
>                 .withB(b)
>                 .withC(C)
>                 .withD(D)
>                 .withE(E)
>                 .withF(F)
>                 .withG(G)
>                 .build();
> }
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

whereby
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.java}
> // perhaps an inner class of Hideous
> public class HideousBuilder {
>
>   // other stuff
>
>   public Hideous build() {
>     return new Hideous(a, b, c, d, e, f, g);
>   }
> }
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now you get the best of both worlds: argument validation and 80 character columns. Hell, you get the added benefit of getting to write a thousand more SLoC and that'll impress your employer, right?

##Haskell
In Haskell, a constructor is a function that returns a datatype.
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> h = Hideous a b c d e f g
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It's a bit more terse than the Java call. We could do something like this:
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}
> infixl 1 $$
> f $$ a = f a
>
> h = Hideous
        $$ a
        $$ b
        $$ c
        $$ d
        $$ e
        $$ f
        $$ g
> ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

##Bullshit
It's a pretty ugly solution: you abuse associativity with an operator no one has seen before. At least in Java we could do argument validation. In Haskell, you'd need to inject a smart constructor. Something like:
