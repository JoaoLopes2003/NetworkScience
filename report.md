\section{Introduction}

When you ask someone about their hobbies or likes, a good portion of the time, they will mention music. And with the rise of internet and streaming platforms, music has become more accessible than ever. The geographical and linguistic barriers that once existed have been broken down, and people and artists are collaborating more than ever. Each day new songs are released, new artists appear, artists decide to explore different genres, to collaborate with new artists, and so on. Overall, this is a very dynamic and ever-changing environment, which makes it very interesting to study.

Considering this, we decided to study the music industry, answering questions like:

\begin{itemize}
    \item Does more songs result in a higher probability of an artist having a popular song?
    \item Does higher number of released songs result in a overall higher popularity of an artist's songs?
    \item Do artists that collaborate with other artists have a higher probability of having a popular song?
    \item Do artists normally do collaborations in their main genre or in other genres?
    \item How do genres relate to each other?
    \item Can we predict the probability of an artist joinig another genre based on their main genre?
\end{itemize}

For this, we used the datatset \textit{Spotify Tracks Dataset}, from Kaggle \cite{spotify_tracks_dataset}, which contains information about songs, artists, albums, genres, etc. This dataset was obtained by the author through the Spotify API and cleaned after.

The dataset contains 113999 rows, corresponding to 113999 songs, and 21 available attributes, being the relevant ones for our study:

\begin{itemize}
    \item \textbf{song name}: name of the song
    \item \textbf{album name}: name of the album
    \item \textbf{duration (ms)}: duration of the song in milliseconds
    \item \textbf{genres}: genre of the song
    \item \textbf{artists involved}: list of artists involved in the song
    \item \textbf{popularity}: popularity of the song, from 0 to 100
\end{itemize}

With these attributes, we organized the information in a graph structure, so we could analyze and answer the questions we proposed. This way, a multigraph was created, where the nodes are the artists and the edges are the collaborations between them. Each edge referes to a single song/collaboration, so if two artists collaborate in multiple songs, there will be multiple edges between them. Also, if the artist releases a solo song, it will be represented as a self-loop in the graph. In addition, the attributes of the edges are the song name, album name, duration, genre (based on the dataset each song only has one genre) and popularity, which also represents the weight of the edge.

\section{Analysis}

\subsection{Basic Statistics}

Regarding the basic statistics, the multigraph created from the 113999 songs, originated \textbf{29858} nodes, or artists, and \textbf{157180} edges. From the edges, \textbf{83924} are self-loops, corresponding to solo songs, and \textbf{30075} are collaborations between artists. In addition, \textbf{15038} artist only have solo songs, \textbf{14820} artists have at least one collaboration.

In relation to the average clustering coefficient and the degree distribution, the first is \textbf{0.27}, which is a relatively high value, indicating that the artists tend to collaborate with other artists that are also connected to them. The degree distribution is shown in Figure \ref{fig:degree_distribution}, where we can see that most artists have a low degree, but there are some artists with a very high degree, indicating that they collaborate with many other artists. Also, we display in Figure \ref{fig:degree_distribution_log} the degree distribution in a log-log scale, fitting a power law distribution, to check if the graph is scale-free. The fitted power law distribution has a slope of \textbf{-2.18} and a $R^2$ value of \textbf{0.92}, indicating a good fit. This could indicate that the graph is scale-free, but we need to be careful with this conclusion, since the dataset is not complete and there are many artists that are not present in it. A more rigorous analysis would be needed to confirm this.

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{degree_distribution.png}
    \caption{Degree distribution of the multigraph.}
    \label{fig:degree_distribution}
\end{figure}

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{degree_distribution_log.png}
    \caption{Degree distribution of the multigraph in a log-log scale.}
    \label{fig:degree_distribution_log}
\end{figure}

About the presence of components in the graph, we have identified 12525 connected components. Their sizes and distribution are shown in Figure \ref{fig:components_size}, which corresponds to a direct screenshot of the output in the notebook we developed. It shows the size of each component, the number of components of that size, and even the average song popularity in the component, which will be important for the next analysis.

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{components_size.png}
    \caption{Size of the connected components in the multigraph.}
    \label{fig:components_size}
\end{figure}

Regarding the giant component we obtained the following statistics:

\begin{itemize}
    \item \textbf{Size}: 12267 artists
    \item \textbf{Number of edges}: 33750
    \item \textbf{Average degree}: 14.51
    \item \textbf{Average clustering coefficient}: 0.4596
    \item \textbf{Average shortest path length}: 8.1744
    \item \textbf{Diameter}: 28
\end{itemize}

The degree distribution can be seen in Figure \ref{fig:giant_component_degree_distribution}.

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{giant_component_degree_distribution.png}
    \caption{Degree distribution of the giant component.}
    \label{fig:giant_component_degree_distribution}
\end{figure}

All these data, highlights, for instance, that the giant component is very connected, as the average clustering coefficient is high, and that the artists in the giant component tend to collaborate with many other artists, as the average degree is also high. Additionally, the average shortest path length is relatively low, indicating that it is easy to reach any artist in the giant component from any other artist.

\subsection{Popularity Analysis}

Regarding popularity, we explored 3 different aspects.

The first one aimed to check if solo songs were more probable to be popular than collaborations. For this, we drew two distributions, one representing how many solo songs had a certain popularity, and the other representing how many collaborations had a certain popularity. Then we normalized the values, because the number of collaborations is much lower than the number of solo songs, and we plotted them. The results are shown in Figure \ref{fig:solo_vs_collab} and Figure \ref{fig:solo_vs_collab_difference}, where the second one shows the difference between the two distributions, highlighting that collaborations tend to be more popular than solo songs, especially in the higher popularity range.

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{solo_vs_collab.png}
    \caption{Popularity distribution of solo songs and collaborations.}
    \label{fig:solo_vs_collab}
\end{figure}

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{solo_vs_collab_difference.png}
    \caption{Difference between the popularity distribution of solo songs and collaborations.}
    \label{fig:solo_vs_collab_difference}
\end{figure}

This could indicate that artists might benefit from collaborating with other artists, since collaborations tend to be more popular than solo songs.

The second aspect we explored was if the size of the component, or in other words the network of artists you are a part of, influences the average popularity of the songs you release. For this, we calculated the average popularity of the songs in each component (in case there were multiple components of the same size, we averaged the popularity of the songs in those components) and plotted it against the size of the component. The results are shown in Figure \ref{fig:component_size_vs_popularity}, where we can see that there is no conclusive correlation between the two variables, as the variations could be due to the fact that there are fewer bigger components, than smaller ones, making their average popularity more volatile.

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{component_size_vs_popularity.png}
    \caption{Average popularity of the songs in each component vs the size of the component.}
    \label{fig:component_size_vs_popularity}
\end{figure}

The last aspect we explored was the relationship between the artist overall popularity and the number of songs they released. We counted the number of songs each artist released, and plotted it against their average popularity. Figure \ref{fig:artist_songs_vs_popularity} shows the results, showing that despite the noise in the data, if we try to fit a linear regression, we can see that there is a negative correlation between the two variables, indicating that artists that release more songs tend to have a lower average popularity. Additionally, we plotted for each number of songs released, the average of the maxium popularity of the songs released, which is shown in Figure \ref{fig:artist_songs_vs_max_popularity} (Figure \ref{fig:artist_songs_vs_max_popularity_binned} shows the same data but binned).

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{artist_songs_vs_popularity.png}
    \caption{Average popularity of the songs released by each artist vs the number of songs released.}
    \label{fig:artist_songs_vs_popularity}
\end{figure}

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{artist_songs_vs_max_popularity.png}
    \caption{Average maximum popularity of the songs released by each artist vs the number of songs released.}
    \label{fig:artist_songs_vs_max_popularity}
\end{figure}

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{artist_songs_vs_max_popularity_binned.png}
    \caption{Average maximum popularity of the songs released by each artist vs the number of songs released (binned).}
    \label{fig:artist_songs_vs_max_popularity_binned}
\end{figure}

The two figures, \ref{fig:artist_songs_vs_popularity} and \ref{fig:artist_songs_vs_max_popularity}, could indicate that despite artists that release more songs tend to have a lower average popularity, they still have the most popular songs. Without more information, this could either mean they obtained this results through the experience they gained from releasing more songs, or that they eventually just got lucky.

\subsection{Genre Analysis}

About the genres, we explored 4 different aspects.

Firstly, we explored how many genres are artists involved in. For this, we counted the number of genres each artist is involved in, then converting the plot into a distribution that represents the probability of an artist being involved in a certain number of genres. The results are shown in Figure \ref{fig:artist_genres}, where we can see that most artists are involved in only one genre, but there are some artists that are involved in multiple genres, indicating that they might be more versatile.

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{artist_genres.png}
    \caption{Distribution of the number of genres artists are involved in.}
    \label{fig:artist_genres}
\end{figure}

Then, we explored if being involved in more than one genre influences the overall popularity of the artist. For this, we plotted the average popularity of the songs released by artists that are involved in a certain number of genres. The results are shown in Figure \ref{fig:artist_genres_vs_popularity}, but they were inconclusive, as there is no clear correlation between the two variables, indicating that being involved in more than one genre does not necessarily mean that the artist will have a higher or lower popularity.

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{artist_genres_vs_popularity.png}
    \caption{Average popularity of the songs released by artists vs the number of genres they are involved in.}
    \label{fig:artist_genres_vs_popularity}
\end{figure}

For the third aspect, we analyzed the genres present in each component, trying to understand, for instance, why the small components didn't connect to the giant component. In Figure \ref{fig:components_genres}, we ilustrate using circular plots, the distribution of the genres in the Top 4 biggest components.

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{components_genres.png}
    \caption{Distribution of the genres in the Top 4 biggest components.}
    \label{fig:components_genres}
\end{figure}

As we can see, the giant component, which incorporates 12267 artists, has a very diverse distribution of genres, with no genre being predominant. On the other hand, all the other components have predominant genres, with the main genre achieving more than 93% of the total songs in the component. This could indicate that these small components are associated with small and closed communities, that enjoy the same genre and collaborate with each other.

Finally, we explored what's the relationship between each two genres in an artist. By other words, if an artist is involved in a certain genre, what is the probability of them being involved in another genre. For this, we followed to approaches.

\begin{itemize}
    \item We calculated the probability of an artist being involved in a certain genre, given that they are involved in another genre, by considering that they are equaly involved in both genres if they are both present in the artist's songs, ignoring the number of songs in each genre. The results are shown in Figure \ref{fig:genres_relationship}, where we can see that the genres are not independent, as there are some genres that are more likely to be present together than others. It's important to mention that we normalized the values, considering the hightest obtained value, and used a threshold, because otherwise it would be hard to compare the strenght of the relationships between the genres and almost every genre would be connect to every other genre, repectively.
    \item We calculated the probability of an artist being involved in a certain genre, given that they are involved in another genre, by considering the number of songs in each genre. This way, for instance, if the artist has 10 songs in genre A and 5 songs in genre B, we consider that they are more involved in genre A than in genre B. The results are shown in Figure \ref{fig:genres_relationship_weighted} and we also normalized the values and used a threshold.
\end{itemize}

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{genres_relationship.png}
    \caption{Probability of an artist being involved in a certain genre, given that they are involved in another genre (unweighted).}
    \label{fig:genres_relationship}
\end{figure}

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{genres_relationship_weighted.png}
    \caption{Probability of an artist being involved in a certain genre, given that they are involved in another genre (weighted).}
    \label{fig:genres_relationship_weighted}
\end{figure}

As expected the results are different, but both highlight some relations that intuitively make sense. For instance, in Figure \ref{fig:genres_relationship}, we can see that the genres \textit{eletro}, \textit{edm} and \textit{house} are very related, as they are all electronic music genres. Also, in Figure \ref{fig:genres_relationship_weighted}, we can also see this same relation, but also others, for example, \textit{pop}, \textit{dance} and \textit{hip-hop}, which are also related genres.

Note that we used the same threshold for both figures, so the values are comparable, but the results are different, as the first one is unweighted and the second one is weighted.

Based on this, we went further, and based on the second approach, we wrote a function that based on a given genre, draws a bubble hierarchy plot, showing the genres that are more likely to be present in artists that are involved in the given genre. With this we can predict the probability of an artist being involved or joining another genre based on them being involved in a certain genre. Two examples of the output are shown in Figure \ref{fig:genres_hierarchy_pop} and Figure \ref{fig:genres_hierarchy_reggaeton}, where we can see, respectively, that artists that are involved in the \textit{pop} genre are more likely to be involved in the \textit{pop-film} and \textit{hip-hop} genres than in the \textit{rock} and \textit{metal} genres, while artists that are involved in the \textit{reggaeton} genre are more likely to be involved in the \textit{latino} and \textit{reggae} genre than in the \textit{eletro} genre. Note that the size of the genres' circles reflect the number of songs with that genre associated.

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{genres_hierarchy_pop.png}
    \caption{Bubble hierarchy plot of the genres related to the pop genre.}
    \label{fig:genres_hierarchy_pop}
\end{figure}

\begin{figure}[H]
    \centering
    \includegraphics[width=0.8\textwidth]{genres_hierarchy_reggaeton.png}
    \caption{Bubble hierarchy plot of the genres related to the reggaeton genre.}
    \label{fig:genres_hierarchy_reggaeton}
\end{figure}