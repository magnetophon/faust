# A NOTE ON HOW TO EXTEND THE COMPILER WITH NEW SIGNAL EXPRESSIONS

The concerned files are:
-   `global.hh` and `global.cpp` where the new symbols are defined
-   `signals.hh` and `signals.cpp` where the new constructors/testers are defined
-   `ppsig.cpp` where pretty printing for the expression is defined
-   `sigtyperules.cpp` where the type of the expression is defined 
-   `subsignals.cpp` where the subsignals of the new expression are computed
-   `signalVisitor.cpp` and `sigIdentity.cpp` where the new expression visit is defined

Additionnaly: `signalSplitter.cpp`, `signalDependencies.cpp`, `delaySimplifier.cpp`  and `compile_scal.cpp` are concerned.

As an example we detail the addition of `sigTableWrite` and `sigTableRead` expressions.

## Symbol definitions

In `global.hh` we add

    Sym SIGTABLEWRITE;
    Sym SIGTABLEREAD;

to the declaration of struct global, 

In `global.cpp` in the global() constructor, we initialize:

    SIGTABLEREAD       = symbol("SigTableRead");
    SIGTABLEWRITE      = symbol("SigTableWrite");

## Expression definitions

    Tree sigTableWrite(Tree id, Tree origin, int tblsize, Tree init, Tree idx, Tree sig);
    bool isSigTableWrite(Tree s, Tree& id, Tree& origin, int* tblsize, Tree& init, Tree& idx, Tree& sig);

    Tree sigTableRead(Tree id, Tree origin, Tree idx);
    bool isSigTableRead(Tree s, Tree& id, Tree& origin, Tree& idx);

## Expression implementations

    Tree sigTableWrite(Tree id, Tree origin, int tblsize, Tree init, Tree idx, Tree sig)
    {
        return tree(gGlobal->SIGTABLEWRITE, id, origin, tree(tblsize), init, idx, sig);
    }

    bool isSigTableWrite(Tree s, Tree& id, Tree& origin, int* tblsize, Tree& init, Tree& idx, Tree& sig)
    {
        Tree tsize;
        if (isTree(s, gGlobal->SIGTABLEWRITE, id, origin, tsize, init, idx, sig)) {
            *tblsize = tree2int(tsize);
            return true;
        } else {
            return false;
        }
    }

    Tree sigTableRead(Tree id, Tree origin, int dmin, Tree idx)
    {
        return tree(gGlobal->SIGTABLEREAD, id, origin, tree(dmin), idx);
    }

    bool isSigTableRead(Tree s, Tree& id, Tree& origin, int* dmin, Tree& idx)
    {
        Tree tmin;
        if (tree(gGlobal->SIGTABLEREAD, id, origin, tmin, idx)) {
            *dmin = tree2int(tmin);
            return true;
        } else {
            return false;
        }
    }
