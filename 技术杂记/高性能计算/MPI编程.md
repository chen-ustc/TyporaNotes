# MPI编程

## 语法知识

### MPI_Scatter和MPI_Scatterv的区别

从原型来看：

```c
int MPI_Scatter(const void		*sendbuf,
			   int			   sendcount,
			   MPI_Datatype	    sendtype,
			   void			   recvbuf,
			   int			   recvcount,
			   MPI_Datatype	    sendtype,
			   int			   root,
			   MPI_Comm		    comm);
```

```c
int  MPI_Scatterv(const void	*sendbuf,
				 const int	   sendcounts[],
				 const int 	   displs[],
				 MPI_Datatype  sendtype,
				 void		  *recvbuf,
				 int		  recvcount,
				 MPI_Datatype  recvtype,
				 int 		  root,
				 MPI_Comm	   comm);
```

可以看到scatterv相较于scatter

