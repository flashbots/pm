# Engineering Projects

```graphviz
digraph hierarchy {

                nodesep=0.5

                node [color=Red,fontname=Courier,shape=box]
                edge [color=Black, style=dashed]

                FlashBots->{Illuminate Democratize Distribute}
                Illuminate->{mev_inspect mev_explore}
                Democratize->{mev_search mev_relay mev_extract}
                mev_extract->mev_geth
}
```