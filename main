function [] = main
    clear;
    
    % Step 3
    [S, Id] = get_scores_from_file;
    
    % Step 4
    [G, I] = Genuine_Impostor(S, Id);

    % FMR, FNMR, DET, ROC, EER
    FMR_FNMR(G, I);    
    
end

function [S, Id] = get_scores_from_file
% Loads score matrix and plots info.

    S = load('Data/scorematrix.txt', '-ascii');
    Id = load('Data/id.txt', '-ascii');
    [np, nt] = size(S);
    nId = max(Id);
    Entries = (1:np);

    fprintf(' Size of score matrix: %u x %u\n',np,nt);
    fprintf(' Number of identities: %u\n', nId);

    figure(1); plot(Entries, Id); 
    xlabel('Entry'); ylabel('Identity'); title('Mapping entry number to identity');

    figure(2); imagesc(S); colormap('gray');
    ylabel('test'); xlabel('reference'); title('Score matrix');
end


function [G, I] = Genuine_Impostor( S, Id )
% 1. Extracts the (I)mposter and (G)enuine scores from the S matrix
% 2. Plots the graph of the G and I.

    % Bin size
    % For the assignment0 use
    % ( 1 < nbins < 20 ) for better results.
    nbins = 10;
    
    % Size1D has the size of each dimension of the
    % S matrix because S is square.
    [Size1D, ~] = size(S);
    
    % Find the max and the min values of the S matrix.
    % These values will be used in order to calculate the appropriate
    % size of the G and I matrices.
    minvalue = 13.9087; % one big value. 
    maxvalue = 2.98956; % one small value
    % The above two variables should be changed to another value for a different scorematrix.
    % Assigning the first value of the matrix may be a good option.
    for i = 1 : Size1D
        for j = i + 1 : Size1D
            if ( S(i,j) < minvalue )
                minvalue = S(i, j);
            end
            if ( S(i,j) > maxvalue )
                maxvalue = S(i, j);
            end
        end
    end
    % minvalue        =  -1.5963e+03
    % int32(minvalue) =  -1596
    
    
    % G and I matrices should be abs(maxvalue) + abs(minvalue) size
    % because they are used in order to store the probability of occurring
    % each simularity score for authorized and unauthorized users.
    G_I_size = int32(abs(maxvalue) + abs(minvalue));
    
    % However, due to the fact that the simularity score is type of double
    % a suitable bin size is used so as to reduce the size of the matrix
    % and categorize the values.    
    real_G_I_size = G_I_size / nbins;
    
    I = zeros(1, real_G_I_size); % Imposter-Score Matrix
    G = zeros(1, real_G_I_size); % Genuine-Score Matrix

    % Total number of measurements for authorized users.
    tmG = 0;
    
    % Total number of measurements for unauthorized users.
    tmI = 0;
    
    % For all similarity scores of the S matrix.
    for i = 1 : Size1D
        for j = i + 1 : Size1D % Avoid duplicate results & diagonal elements.
            
            % For each bin starting from the minimum value of the score
            % matrix up to the maximum value, where bin size is defined.
            for w = 1 : real_G_I_size
                s = w * nbins - abs(minvalue) ;
                
                % Checks if the value S(i,j) corresponds to specific bin s.
                if (S(i, j) > s-nbins && S(i, j) <= s )
                    
                    % If the two samples are not from the same person
                    % add similarity score in the Impostor matrix.
                    if ( Id(j) ~= Id(i) )
                        I(1, w) = I(1, w) + 1;
                        tmI = tmI + 1;
                        
                        % If the two samples are from the same person
                        % add similarity score in the Genuine matrix.
                    else
                        G(1, w) = G(1, w) + 1;
                        tmG = tmG + 1;
                    end
                    
                    % S(i, j) is already in a bin, therefore we should not
                    % check if it corresponts to another.
                    break
                end
            end
        end
    end
    
    % Normalized all the values with the total number
    % of inquiries for each circumstance
    for i = 1 : real_G_I_size
        I(1, i) = I(1, i) / tmI;
        G(1, i) = G(1, i) / tmG;
    end
    
    figure(3)
    
    % Plot.
    plot(G, 'r');
    hold on;
    plot(I, 'g');
    
    % Header.
    title('Genuine & Impostor Frequency Diagram');
    
    % Labels.
    xlabel('Similarity score');
    ylabel('Frequency');
    legend('Genuines', 'Impostors', 'Location', 'Best');    
    
    % Custom x-axix
    temp = ((abs(minvalue)+maxvalue) / 11);
    set(gca,'XTickLabel', [ minvalue ; minvalue + temp ; minvalue + temp * 2;
        minvalue + temp * 3 ; minvalue + temp * 4 ; minvalue + temp * 5 ;
        minvalue + temp * 6 ; minvalue + temp * 7 ; minvalue + temp * 8 ;
        minvalue + temp * 9 ; maxvalue ])

    %% Normal plot is not used because we have plot a figure with custom x-axis
    %     figure(3)
    %     % Plot.
    %     plot(G, 'r');
    %     hold on;
    %     plot(I, 'g');
    %     
    %     % Header.
    %     title('Genuine & Impostor Frequency Diagram');    
    %     
    %     % Labes.
    %     legend('Genuines', 'Impostors', 'Location', 'Best');
    %     xlabel('Similarity score (shifted to start at 0 value');
    %     ylabel('Frequency');

end

function [] = FMR_FNMR(G, I)

    % Get and save the sizes of I, G.
    % Due to the fact that they have the same size
    % we use only one variable for both of them.
    [~, K] = size(I);
    
    % Calculate FMR for each threshold value.
    FMR = zeros(1, K);
    for i = 1 : K
        pn = 0;
        for j = 1 : (i-1)
            pn = pn + I(j);
        end
        FMR(1, i) = 1 - pn;
    end
    
    % Calculate FNMR for each threshold value.
    FNMR = zeros(1, K);
    for i = 1 : K
        pb = 0;
        for j = 1 : (i-1)
            pb = pb + G(j);
        end
        FNMR(1, i) = pb;
    end
    
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
    %%%%%%               PLOTS                     %%%%%%%%%
    %%%%%% Locate_EER_spot, Plot, Header, Labels   %%%%%%%%%
    %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
    
    %%% start "EER from FMR - FNMR intersection" %%%
    figure (6)
   
    % Locate EER spot.
    templowest = 5.6211;
    lowestValue = 0;
    lowestK = 0;
    % Find the index for which FMR(index) and 
    % FNMR(index) have the lowest value.
    for i = 3 : K-2
        if ( abs(FMR(i) - FNMR(i)) < templowest )
            templowest = abs(FMR(i) - FNMR(i));
            lowestK = i;
            lowestValue = FMR(i);
        end
    end
    
    % Plot.
    plot(FMR, 'r');
    hold on;
    plot(FNMR, 'g');
    hold on;
    plot(lowestK, lowestValue, 'ro', 'MarkerSize', 12); % EER mark.
    hold on;
    
    % Header.
    title('EER from FMR - FNMR intersection');
    
    % Labels.
    legend('False Match Rate', 'False Non-Match Rate', 'EER', 'Location', 'Best');
    xlabel('Threshold');
    ylabel('Εrror rate');
    %%% end of "EER from FMR - FNMR intersection" %%%
    
    
    %%% start of plotting "EER from a ROC curve" %%%
    figure (7)
    
    % Locate EER spot.
    idx = find(abs(FMR - FNMR) < 0.02, 1); % find intersection with x=y
    px = FMR(idx); % save the x value
    py = FNMR(idx); % save the y value
    
    % Plot.
    plot(FMR,FNMR, 'r');
    hold on;    
    plot(px, py, 'ro', 'MarkerSize', 12); % EER mark.
    line([0 1],[0 1]);

    % Header.
    title('EER from a ROC curve');
    
    % Labels.
    legend('ROC', 'EER', 'x = y', 'Location', 'Best');
    xlabel('FMR');
    ylabel('FNMR');
    %%% end of "EER from a ROC curve" %%%
    
    
    %%% start of plotting "EER from a ROC curve ( logarithmic scale )" %%%
    figure (8)

    % Locate EER spot.
    idx = find(abs(log(FMR) - log(FNMR)) < 0.15, 1); % find intersection with x=y
    px = FMR(idx); % save the x value
    py = FNMR(idx); % save the y value
    
    % Plot.
    loglog(FMR, FNMR, px, py, 'ro', 'MarkerSize', 12); % EER mark.
    %%line([0 1],[0 1]); does not work with loglog

    % Header.
    title('EER from a ROC curve ( logarithmic scale )');
        
    % Labels.
    legend('ROC', 'EER', 'Location', 'Best');
    xlabel('FMR');
    ylabel('FNMR');
    %%% end of "EER from a ROC curve ( logarithmic scale )" %%%
    
    
    %%% start of plotting "1-FNMR against FMR" %%%
    figure (9)

    % Plot.
    plot( 1 - FNMR, FMR , 'r' )
    line([0 1],[1 0]);

    % Header.
    title('1-FNMR against FMR');
        
    % Labels.
    legend('1-FNMR against FMR', 'x = y', 'Location', 'Best');
    xlabel('FNMR');
    ylabel('FMR');
    %%% end of "1-FNMR against FMR" %%%    

end
