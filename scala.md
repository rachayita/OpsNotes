- A trait containing multiple abstract members is sometimes called an environment. 
This is because the trait contains the environment needed for another class to function.

- when the compiler is looking for type associations, it uses the type it’s attempting to
convert from, not the type it’s attempting to convert to.

- when lazy vals are evaluated, in order to preserve thread-safety, the Scala compiler will lock the whole surrounding class, whereas Eval will only lock itself.
